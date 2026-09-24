# Adaptive Relaxation-Based Nonconservative Chance Constrained Stochastic MPC

Avik Ghosh , Graduate Student Member, IEEE, Cristian Cortes-Aguirre Yi-An Chen ID Adil Khurram D , Member, IEEE, and Jan Kleissl

Abstract— Chance constrained stochastic model predictive controllers (CC-SMPCs) tradeoff full constraint satisfaction for economical plant performance under uncertainty. Previous CC-SMPC works are over-conservative in constraint violations leading to worse economic performance. Other past works require a priori information about the uncertainty set, limiting their application. This article considers a discrete linear timeinvariant (LTI) system with hard constraints on inputs and chance constraints on states, with unknown uncertainty distribution, statistics, or samples. This work proposes a novel adaptive online update rule to relax the state constraints based on the time average of past constraint violations, to achieve reduced conservativeness in closed-loop. Under an ideal control policy assumption, it is proven that the time average of constraint violations asymptotically converges to the maximum allowed violation probability. The method is applied for optimal battery energy storage system (BESS) dispatch in a grid-connected microgrid (MG) with photovoltaic (PV) generation and load demand, with chance constraints on BESS state of charge (SOC). Realistic simulations show the superior electricity cost-saving potential of the proposed method as compared with the traditional economic model predictive control (EMPC) without chance constraints, and a state-of-the-art approach with chance constraints. We satisfy the chance constraints nonconservatively in closed-loop, effectively trading off increased cost savings with minimal adverse effects on BESS lifetime.

Index Terms— Battery energy storage systems (BESSs), chance constraints, discrete linear time invariant (LTI) systems, forecast uncertainty, microgrids (MGs), nonconservative, stochastic model predictive control (SMPC), uncertainties.

NOMENCLATURE

<table><tr><td> $\alpha$ </td><td>Maximum probability of violation of state constraints.</td></tr><tr><td> $\gamma$ </td><td>Constant of proportionality in online  $h$  update rule.</td></tr><tr><td> $\hat{w}$ </td><td>Width of the critical region.</td></tr><tr><td> $\kappa$  and  $\kappa'$ </td><td>Critical region.</td></tr><tr><td> $\mathbb{P}$ </td><td>Probability measure.</td></tr><tr><td> $\mathcal{F}_{t}$ </td><td>Filtration.</td></tr></table>

Received 28 October 2024; revised 19 December 2024; accepted 12 February 2025. Date of publication 17 March 2025; date of current version 20 August 2025. This work was supported by the California Energy Commission under Contract EPC-17-049. Recommended by Associate Editor F. You. (Corresponding author: Avik Ghosh.)

Avik Ghosh, Cristian Cortes-Aguirre, Yi-An Chen, and Adil Khurram are with the Department of Mechanical and Aerospace Engineering, University of California at San Diego (UCSD), San Diego, CA 92093 USA (e-mail: avghosh@ucsd.edu; ccortesa@ucsd.edu; yic002@ucsd.edu; akhurram@ucsd.edu).

Jan Kleissl is with the Center for Energy Research, Department of Mechanical and Aerospace Engineering, UCSD, San Diego, CA 92093 USA (e-mail: jkleissl@ucsd.edu).

Digital Object Identifier 10.1109/TCST.2025.3547260

h˜ Adaptive state constraint tightening parameter.  
A System state transition matrix.  
B System control input matrix.  
d Control input coupling vector dimension.  
E System state uncertainty matrix.  
F Control input uncertainty matrix.  
G State chance constraint matrix.  
g State chance constraint vector.  
h Adaptive state constraint relaxing parameter.  
k, t Time index.  
M Control input coupling matrix.  
m Control input dimension.  
N MPC prediction horizon length.  
n State dimension.  
p Uncertainty dimension/probability of violation of state constraints.  
q Control input constraint vector dimension.  
r State chance constraint vector dimension.  
S Control input constraint matrix.  
s Control input constraint vector.  
u Control input.  
V State constraint violation tracker.  
w Uncertainty.  
x State.  
Y Time average of state constraint violations.  
Z Absolute difference between α and Y .  
c Control input coupling vector.  
NCDP Noncoincident demand peak.  
OPDP On-peak demand peak.  
BESS Battery energy storage system.  
JCCs Joint chance constraints.  
LTI Linear time invariant.  
MG Microgrid.  
MPC Model predictive control.  
NCDC Noncoincident demand charge.  
OPDC OP demand charge.  
PV Photovoltaic.  
SMPC Stochastic MPC.  
SOC State of charge.  
VRES Variable renewable energy sources.

## I. INTRODUCTION

## A. Motivation

URRENTLY, there is great emphasis on integrating VRES, such as wind and PV generators into the electricity grid, with the goal of decarbonizing power production.

There is, however, an intermittent nature to VRES, which can potentially lead to power imbalance in the electric grid, thereby risking grid stability [1]. BESS can minimize power fluctuations caused by the integration of VRES into the grid [1] and can be additionally used for energy arbitrage, peak load shaving, valley-filling, and ancillary services. However, to maximize the benefits from installing BESS, optimal BESS scheduling strategies need to be devised to maximize electricity bill savings, while providing services to the grid.

It is possible to optimally dispatch BESS, utilizing MPC based scheduling algorithms that include grid constraints. However, uncertainty in forecasts can significantly reduce performance and should be taken into account when formulating MPCs. Classical open-loop min–max formulation based robust MPC can be used to factor in uncertainty but it leads to over-conservative solutions which may not be economical from an operational perspective [2]. Other variations of robust MPC such as closed-loop min–max formulation (commonly known as “feedback MPC”) suffer from prohibitive complexity [2]. Tube-based MPC requires specification of a bounded uncertainty set a priori [3] (a problem in common with robust MPC), which may be difficult to specify nonconservatively for a complex practical system such as a VRES integrated MG, which involves a variety of forecasts. An over-conservative uncertainty set negatively impacts economical system performance.

SMPC methods based on chance constraints strike a tradeoff between economic operation and full constraint satisfaction [3]. Chance constraints allow for the MPC to operate in a more economical way by respecting a maximum probability of constraint violation. The superior economic performance, lower complexity, and weaker assumption requirements of chance constrained SMPC are desirable for BESS operation in VRES-intensive MG, especially under uncertainty in VRES and load forecasts, and is thus the primary focus of this work.

## B. Literature Review

Chance constrained SMPC algorithms have found applications in problems involving building climate control [3], [4], [5], [6], optimal power flow [7], and optimal MG dispatch [8], [9], [10], [11], [12], [13], [14], [15], [16], [17], [18]. Chanceconstrained SMPC problems are solved by converting them into an approximate deterministic form. If the uncertainties are Gaussian, or follow other known distributions [19], [20], standard procedures exist to convert the stochastic problem into a deterministic one. However, in practical scenarios such as VRES and load forecasts, the uncertainty distributions, and additionally, uncertainty statistics (moments such as mean, variance, and skewness) may be unknown and can vary with time (i.e., seasonally/yearly). Other methods of reformulating the SMPC problem into a deterministic one, such as using Chebyshev inequalities [21] require a priori knowledge about the uncertainty statistics (mean and covariance), while using Chernoff bounds suffer from high conservatism [3]. Samplingbased approaches [22], [23] suffer from high computational demand and may require a prohibitive number of samples [3].

Chance constraints are generally enforced pointwise-intime within an MPC prediction horizon, without including past behavior of the system, which can also lead to over-conservativeness (i.e., less than desired constraint violations) in closed-loop [4], [6]. However, reducing over-conservativeness is of paramount importance for MG operators to reduce electricity costs. Thus, in this work we reinterpret the pointwise-in-time chance constraints as the time average of violations (or time average of some loss function of violations) in closed-loop similar to [3], [4], [6], [24], and [25], and first focus our literature review specifically on such online chance constrained SMPC methods with theoretical advancements. The reinterpretation keeps the spirit of occasional constraint violations of the original chance constraint [4] while keeping the memory of past behavior of the system to aid in reducing over-conservativeness in closed-loop.

The work in [6] adaptively relaxed and tightened the MPC state constraints online aided by the amount of violations quantified by a loss function empirically weighted averaged over time. The authors defined a family of stochastic robust control invariant (SRCI) sets for implementing their control online and proved that the empirical weighted average loss is bounded either in expected value or robustly with probability 1, and derived bounds on the convergence time. However, drawbacks of the work include the high computational cost to parameterize the SRCI sets and a priori knowledge about the distribution/statistics of the uncertainty.

Fleming and Cannon [24] adaptively relaxed the MPC state constraints online based on the time-average of: 1) the number of constraint violations in the first method and 2) a loss metric based on a convex loss function of constraint violations in the other method. Instead of providing asymptotic bounds on constraint violations as done in previous works like [3] and [4], the authors provided stronger robust bounds in closed-loop over finite time periods. A practical limitation of [24] for our application (economic MG dispatch) is the assumption of an objective function that is composed of stage costs with quadratic penalties on predicted control inputs and state deviations from an a priori defined robust positively invariant target set. Construction of a nonconservative robust positively invariant target state set is difficult. In addition, electricity costs due to grid imports in an MG with BESS cannot be expressed by stage costs with quadratic penalties on control inputs and state deviations, because economic stage costs are not necessarily positive definite with respect to the target set of states and/or control inputs [26].1 Moreover, the uncertainty is incorporated by bounding the predicted states in state tubes which require a priori specification of the uncertainty set, and

1Economic MG dispatch involves the usage of the electricity cost function directly as the objective function of the MPC controller. Electricity costs incurred by the MG to the utility involve time-of-use energy charges and demand charges. Energy charges (U.S. \$/kWh) are incurred based on the volumetric import of electricity from the grid, while demand charges (U.S. \$/kW) are incurred based on the maximum load import from the grid over the month. Two distinct time-of-use demand charges are used: one based on maximum grid imports for the whole month called NCDP, added on the top of maximum grid import between 16:00 and 21:00 h of all days of the month, called OPDP. The demand charges associated with NCDP and OPDP are called NCDC and OPDC, respectively. For commercial and industrial customers, demand charge costs are typically 30%–70% of the monthly electricity costs [27].

the computation time is similar to a corresponding robust MPC problem which is more expensive than the nominal MPC.

Oldewurtel et al. [3], Muñoz-Carpintero et al. [4], and Capone et al. [25] adaptively tightened the state constraints online during MPC computations: 1) using an update rule based on the time average of past state constraint violations in [3] and [4] and 2) by iteratively employing a data-driven Gaussian process binary regression-based approach depending on the observed state constraint violations in the training data in [25]. In [4], using stochastic approximation, the authors also proved the convergence of the time-average of constraint violations in probability to the allowable “least conservative” level. A few limitations of [4] are the a priori assumption of the uncertainty distribution, and the strong assumption of terminal stability region of the state in closed-loop, which is unrealistic for economic MG dispatch using BESS. While [25] relaxes the requirement of a priori knowledge of the uncertainty distribution, iteratively learning the final optimal tightening parameter is data intensive. The entire solution framework exhibits significantly more computation cost for satisfying the chance constraints in the long run as compared with the nominal MPC. For our application, [25] may be unable to perform economically or may cause significant violation of the chance constraints if the underlying uncertainty distribution changes with time (as the final optimal tightening parameter is learned from past data), which is undesirable for economic MG operation.

Some of the limitations of [4], [6], [24], and [25] are avoided by [3] which can be applied to systems with unknown uncertainty distribution/statistics. Also, [3] does not need the assumptions of terminal stability of the state and the type of the objective function (except convexity assumptions), and the implementation of the SMPC is computationally inexpensive and has a similar computation cost as that of a nominal MPC. The above-mentioned properties make the work in [3] ideal for economic MG dispatch and forms the primary reference on which we develop our work.

Oldewurtel et al. [3] developed an adaptive state constraint tightening rule that allows the time average of violations of the system to converge to the maximum allowable violation probability in a closed-loop under an ideal control policy assumption (which may be unmet for practical implementation). However, as clarified by the authors, the convergence was argued intuitively without rigorous proof. Also, despite its advantages, [3] is still not robust to significant violation of chance constraints under time-varying uncertainty distribution.

While significant theoretical advancements have been made to develop SMPC with varying degrees of simplifying assumptions, computational cost, and ability to avoid over-conservativeness in closed-loop chance constraint satisfaction, there exists a gap in the literature of exploiting these nonconservative methods for economic MG dispatch under uncertainty. Some recent applied works involving economic MG dispatch in VRES intensive grids with chance constraints are explored in [8], [9], [10], [11], [12], [13], [14], [15], and [16].

None of the works [8], [9], [10], [11], [12], [13], [14], [15], [16] considered demand charges in their electricity cost. The work in [8] required extra steps to generate scenarios of uncertainty at every time step for the MPC prediction horizon and considered the scenarios to be Gaussian. The works [9], [11], [12], [14], [15] considered the uncertainties to be Gaussian or other commonly known distributions, which significantly limits the practical application of these studies. Yuan et al. [10] and Ding et al. [13] employed ambiguity sets to model the uncertainties from historical data and employed distributionally robust chance constrained (DRCC) optimization. Ciftci et al. [16] used adaptive kernel density estimation to estimate the nonparametric uncertainty distribution for VRES from historical data and adjusted the confidence levels according to estimated uncertainties to ensure constraint satisfaction within predefined confidence levels. However, the approaches in [10], [13], and [16] are data intensive and the performance is substantially influenced by the quality and volume of historical data available.

To tackle the aforementioned problems, [17] and the authors of this article in a previous work [18] presented an online adaptive SMPC model inspired by [3]. Guo et al. [17] and Ghosh et al. [18] minimized the VRES integrated MG operating cost, and satisfied chance constraints on states (BESS SOC) in closed-loop without making any assumption about the probability distribution or statistics of the uncertainty. To further reduce over-conservativeness, Guo et al. [17] and Ghosh et al. [18] employed online adaptive constraint relaxation in the nominal MPC. However, the online adaptive constraint relaxation rules used in [17] and [18] are based on intuition having no convergence guarantees or theoretical analyses, and both the works are application specific. From an economic MG dispatch perspective, only [18] included demand charges, while [17] did not. Guo et al. [17] used sample historical data of uncertainties for initial constraint relaxation which [18] avoided by practical engineering approximation. In addition, Guo et al. [17] and Ghosh et al. [18] considered aggregate constraint violations, without any preference for the time when violations should occur. For economic MG dispatch with demand charges, it becomes critical, if necessary, to preferentially be able to violate BESS state constraints during a predefined OP period from 16:00 to 21:00 h, to reduce grid import power peaks, as OPDC is charged on top of NCDC.

The present work is an extension of the previous work Ghosh et al. [18], presented in a generic discrete LTI setting with additional convergence properties and proofs, related theoretical analyses, and additional case studies. The proposed online adaptive SMPC (OA-SMPC) minimizes a generic convex cost function over a finite receding horizon, subject to hard input constraints and chance constraints on states. After presenting the theoretical results of the OA-SMPC, a case study is presented for a grid-connected MG operation with PV, load, and BESS using realistic data for a full year of operation in an economic MPC (EMPC) framework. The performance of the OA-SMPC is compared with a traditional EMPC without chance constraints, and a state-of-the-art approach from the literature with chance constraints having similar computational cost [3]. The OA-SMPC outperforms both the methods with respect to the cost saving potential and nonconservative satisfaction of chance constraints.

## C. Contributions

The contributions of the present work are as follows.

1) To the best of the author’s knowledge, the present work’s adaptive state constraint relaxation framework is limited in the literature as compared to the more common adaptive state constraint tightening. Under the novel adaptive relaxation rule of the present work, it is proven that the time average of the constraint violations asymptotically converges to the maximum allowable violation probability under an ideal control policy assumption similar to [3]. However, a rigorous proof is provided here which was not provided in [3]. Also, for practical implementation (i.e., without the simplifying ideal control policy assumption), while the proposed method cannot guarantee the aforementioned convergence, it still encourages it.  
2) The present work also proves that the time average of the constraint violations exhibits martingale-like behavior asymptotically for practical implementation.  
3) The present work does not require either any a priori assumption about the probability distribution of the uncertainty set or its statistics, or sample uncertainties from historical data. The present work is also robust to significant violation of chance constraints under time-varying uncertainty distribution for practical implementation, provided an additional postprocessing step is incorporated.  
4) The present work incorporates operational adjustments in the online adaptive relaxation rule to account for temporal preference in a state constraint violation, which is critical for economic MG dispatch. In addition, unlike the methods in [3] and [17], the present method prevents excessive overcharging/overdischarging of the BESS to correct for large forecast uncertainties in real-time by the postprocessing step, which otherwise might harm the BESS and leave the MG vulnerable for future demand peaks.  
5) The majority of the earlier works for chance constrained SMPC-based economic MG dispatch presented results over a short time (24 h), or one or two months. However, for MG operators, it is important to have at least year-long studies to determine how the algorithm performs under realistic seasonal variations in loads, VRES generation, and forecasts, a gap which the present work fills.

The rest of this article is organized as follows. Section II presents the notations and standard definitions. Section III introduces the original SMPC problem formulation with chance constraints, approximates the original formulation to frame the OA-SMPC formulation, along with presenting the convergence proofs and related theoretical analysis. Section IV presents the case study for a realistic grid connected MG with PV, load, and BESS, with results and discussion in Section V. Section VI concludes this article summarizing the takeaways of the study.

## II. MATHEMATICAL PRELIMINARIES

## A. Notations

The set of n-tuple of real numbers is denoted by $\mathbb { R } ^ { n }$ . Positive and negative real number sets are denoted by $\mathbb { R } _ { 0 + }$ and $\mathbb { R } _ { 0 - }$ , respectively. The set of natural numbers including 0 is denoted by N. A set of consecutive natural numbers $\{ i , i + 1 , \dotsc , j \}$ is denoted by $\mathbb { N } _ { i } ^ { j }$ . The n-tuple of ones is denoted by $\mathbb { 1 } ^ { n } .$ . States, control inputs, and uncertainties are denoted by $x \in \mathbb { X } \subseteq R ^ { n } , u \in \mathbb { U } \subset \mathbb { R } ^ { m }$ , and $w \in \mathbb { W } \subset \mathbb { R } ^ { p }$ , respectively. The prediction horizon of the MPC is denoted by $N \in \mathbb { N }$ . Actual states at time $t \in \mathbb { T } \subseteq \mathbb { N }$ are denoted by $x ( t )$ , while predicted states, obtained at t by MPC computation ${ \boldsymbol k } \in  { \mathbb { N } } _ { 1 } ^ { N }$ time steps in the future are denoted by $x ( t + k | t )$ . Similarly, predicted control inputs over the MPC prediction horizon are denoted by $u ( t + k | t )$ , with $k \in \mathbb { N } _ { 0 } ^ { N - 1 }$ . An ordered collection of vectors (such as states) over the MPC prediction horizon obtained at time t is denoted by bold letters, $\mathbf { x } ( t { + } 1 ) : =$ $\Big ( x ( t + 1 | t ) , x ( t + 2 | t ) , \dots , x ( t + N | t ) \Big )$ . For matrices A and B of equal dimensions, the operators $\{ < , \leq , = , > , \geq \}$ hold componentwise. The right inverse of a matrix $A \in \mathbb { R } ^ { m \times n }$ with rank $m \ : < n$ is denoted by $A ^ { \dagger }$ . The ith row, and the element from the ith row and jth column of a matrix A is denoted by $A _ { i }$ and $A _ { i j }$ , respectively, while the ith element of a vector x is denoted by $x _ { i } ,$ unless mentioned otherwise. A vector of the first $a \in \mathbb { N }$ elements of a vector x is denoted by $x _ { 1 : a } .$ The expected value of a random variable $Z$ is denoted by E[Z]. |x| denotes the one-norm of a vector x. The “logical NOT,” and “logical $\mathbf { A N D } ^ { \prime \prime }$ operators are denoted by ¬ and $\land ,$ respectively.

## B. Standard Definitions

Definition 1 (Filtered Probability Space [28]): A filtered probability space is defined by $( \Omega , \mathcal { F } , \{ \mathcal { F } _ { t } \} , \mathbb { P } ) . ~ ( \Omega , \mathcal { F } , \mathbb { P } )$ is a probability triple with sample space , σ -algebra (event space) ${ \mathcal F } ,$ , and probability measure P on (, F). $\mathcal { F } _ { t }$ is a filtration, which is an increasing family of sub σ -algebras of $\mathcal { F }$ such that $\mathcal { F } _ { s } \subseteq \mathcal { F } _ { t } \subseteq \mathcal { F } , \forall t \geq s$ , where $t , s \in \mathbb { T }$ .

Definition 2 (Almost Surely [28]): An event $E \in { \mathcal { F } }$ happens almost surely if $\mathbb { P } ( E ) = 1$ . It is denoted by a.s.

Definition 3 (Adapted Stochastic Process $I 2 8 J ) .$ : A stochastic process $Z : = ( Z ( t ) : t > 0 )$ , is called adapted to the filtration $\{ \mathcal { F } _ { t } \}$ if $Z ( t )$ is $\mathcal { F } _ { t }$ measurable ∀t.

Definition 4 (Supermartingale [28]): A stochastic process Z is called a discrete-time supermartingale relative to $( \{ \mathcal { F } _ { t } \} , \mathbb { P } )$ if it satisfies the following.

1) Z is an adapted process.  
2) $\mathbb { E } [ | Z ( t ) | ] < \infty , \forall t .$  
3) $\mathbb { E } [ Z ( t + 1 ) | { \mathcal { F } } _ { t } ] \leq Z ( t ) , { \mathrm { a . s . ~ } } \forall t .$

A discrete-time martingale Z relative to $( \{ \mathcal { F } _ { t } \} , \mathbb { P } )$ is defined similarly, with 3) replaced by $\mathbb { E } [ Z ( t + 1 ) | \mathcal { F } _ { t } ] = Z ( t ) , \mathrm { a . s . } ^ { }$ ∀t .

Definition 5 (Monotone Convergence Theorem for Decreasing Sequence [29]): Let $X = ( x _ { n } : n \in \mathbb { N } )$ be a sequence of real numbers which is monotonically decreasing in the sense that $x _ { n + 1 } \leq x _ { n } , \forall n$ , then the sequence converges if and only if it is bounded, and in which case $\begin{array} { r } { \operatorname* { l i m } _ { n \to \infty } x _ { n } = \operatorname* { i n f } \{ x _ { n } \} } \end{array}$ .

## III. PROBLEM FORMULATION

## A. System Description

The dynamics of the discrete LTI system are governed by

$$
x (t + 1) = A x (t) + B u (t) + E w (t) \quad \forall t \tag {1}
$$

where $A \in \mathbb { R } ^ { n \times n } , B \in \mathbb { R } ^ { n \times m }$ , and $E \in \mathbb { R } ^ { n \times p }$ .

Assumption 1 (System): (a) At each time t, a measurement of the state is available. (b) The set of admissible control inputs U and states X are polytopes containing the origin.

Assumption 2 (Uncertainties): The set of uncertainties W is bounded and contains the origin.

In this setup, $\mathcal { F } = \sigma ( \{ w : w ( t ) \in \mathbb { W } \} : t \in \mathbb { T } )$ , and $\mathcal { F } _ { t } =$ $\sigma ( \{ w ( s ) : w ( s ) \in \mathbb { W } \} : s < t )$ . The system is subject to hard control input constraints and chance constraints on states. The control input constraints are formulated as follows:

$$
S u (t) \leq s \quad \forall t \tag {2}
$$

where $S ~ \in ~ \mathbb { R } ^ { q \times m }$ and $s ~ \in ~  { \mathbb { R } } ^ { q }$ . The time-varying equality constraints coupling the control inputs are formulated as follows:

$$
M u (t) = c (t) + F w (t) \quad \forall t \tag {3}
$$

where $M \ \in \ \mathbb { R } ^ { d \times m } , \ c \ \in \ \mathbb { R } ^ { d }$ , and $\boldsymbol { F } ~ \in ~ \mathbb { R } ^ { d \times p }$ . In previous works like [3], (3) is not considered but for applications such as economic MG dispatch, (3) is important for incorporating physical constraints such as power balance of the MG with the main grid (discussed in detail in Section III-F). However, if (3) is considered in the problem formulation, the $E w ( t )$ term in the RHS of (1) is dropped as the $F w ( t )$ term in the RHS of (3) accommodates the uncertainty.2 In addition, note that constraint (3) is application specific and is independent of the method and theoretical results presented in this article. The chance constraints on the states are formulated as follows:

$$
\mathbb {P} [ G x (t) \leq g ] \geq \bar {1} - \bar {\alpha} \quad \forall t \tag {4}
$$

where $G \in \mathbb { R } ^ { r \times n } , \ g \in \mathbb { R } ^ { r }$ , and $\bar { 1 } = \mathbb { 1 } ^ { r }$ for individual chance constraints. $\bar { \alpha } = [ \alpha _ { 1 } , \ldots , \alpha _ { r } ] ^ { \top }$ is the vector of the pointwisein-time maximum probability of constraint violation, where $\alpha _ { i } \ \in \ ( 0 , 0 . 5 ) \ \forall i \ \in \ \mathbb { N } _ { 1 } ^ { r } .$ . In the individual chance constraint form, (4) can be expressed as, P $\begin{array} { r } { [ G _ { i } x \leq g _ { i } ] \geq 1 - \alpha _ { i } , \quad \forall i \in \mathcal { } } \end{array}$ Nr . In the JCC form, a single violation probability denoted by $\alpha \in ( 0 , 0 . 5 )$ can be defined for simultaneous satisfaction of all state constraints as follows:

$$
\mathbb {P} \left[ G _ {1} x \leq g _ {1} \wedge G _ {2} x \leq g _ {2} \wedge \dots \wedge G _ {r} x \leq g _ {r} \right] \geq 1 - \alpha .
$$

Note that in this work, we reinterpret the maximum probability of violation of state constraints pointwise-in-time given by the chance constraints (4) as the maximum time average of state constraint violations in closed-loop similar to [3], [4], [6], [24], and [25]. $G x ( t ) \leq g$ is referred to as the original constraint with respect to which violations are measured.

2The E matrix is still required for assigning a unique control input after accommodating the uncertainty in closed-loop for multi-input systems, see details in Section III-F.

## B. Online Adaptive SMPC (OA-SMPC)

Over the MPC prediction horizon N , computed from time t, we define the ordered collection of states, control inputs, uncertainties, and coupling vectors as follows:

$$
\mathbf {x} (t + 1) := \left(x (t + 1 | t), x (t + 2 | t), \dots , x (t + N | t)\right) ^ {\top} \in \mathbb {R} ^ {N n}
$$

$$
\mathbf {u} (t) := \left(u (t | t), u (t + 1 | t), \dots , u (t + N - 1 | t)\right) ^ {\top} \in \mathbb {R} ^ {N m}
$$

$$
\mathbf {w} (t) := \left(w (t | t), w (t + 1 | t), \dots , w (t + N - 1 | t)\right) ^ {\top} \in \mathbb {R} ^ {N p}
$$

$$
\mathbf {c} (t) := \left(c (t | t), c (t + 1 | t), \dots , c (t + N - 1 | t)\right) ^ {\top} \in \mathbb {R} ^ {N d}.
$$

The system dynamics can be written in expanded form as follows:

$$
\begin{array}{l} x (t + k | t) = A ^ {k} x (t | t) + \sum_ {i = 0} ^ {k - 1} A ^ {k - 1 - i} B u (t + i | t) \\ + \sum_ {i = 0} ^ {k - 1} A ^ {k - 1 - i} E w (t + i | t) \quad \forall k \in \mathbb {N} _ {1} ^ {N} \forall t \tag {5} \\ \end{array}
$$

where $x ( t | t ) = x ( t )$ . Writing (5) in compact form yields

$$
\mathbf {x} (t + 1) = \mathbf {A} x (t) + \mathbf {B} \mathbf {u} (t) + \mathbf {E} \mathbf {w} (t) \quad \forall t \tag {6}
$$

where $\mathbf { A } \in \mathbb { R } ^ { N n \times n } , \mathbf { B } \in \mathbb { R } ^ { N n \times N m }$ , and $\mathbf { E } \in \mathbb { R } ^ { N n \times N p }$ .

The hard control input constraints over the MPC prediction horizon are formulated as follows:

$$
S u (t + k | t) \leq s \quad \forall k \in \mathbb {N} _ {0} ^ {N - 1} \forall t. \tag {7}
$$

The equality constraints coupling the control inputs over the MPC prediction horizon are formulated as follows:

$$
M u (t + k \mid t) = c (t + k \mid t) + F w (t + k \mid t) \quad \forall k \in \mathbb {N} _ {0} ^ {N - 1} \forall t. \tag {8}
$$

Generally, the chance constraints in (4) are interpreted for the MPC prediction horizon pointwise-in-time by (9a), which is over-conservative in closed-loop (see Remark 1). The corresponding relaxed deterministic reformulation of (9a) as implemented in the MPC prediction horizon by some previous works [3], [4] is given by (9b). The aim of the deterministic reformulation is to tighten the state constraints under nominal MPC computations (resulting from ignoring uncertainties, i.e., $\mathbf { w } ( t ) = \mathbf { 0 } )$ by an adaptive tightening parameter $\tilde { h } \in \mathbb { R } ^ { r }$ given by

$$
\mathbb {P} [ G x (t + k | t) \leq g ] \geq \bar {1} - \bar {\alpha} \quad \forall k \in \mathbb {N} _ {1} ^ {N} \forall t \tag {9a}
$$

$$
G x (t + k \mid t) \leq g - \tilde {h} (t + k \mid t) \quad \forall k \in \mathbb {N} _ {1} ^ {N} \forall t, \mathbf {w} (t) = \mathbf {0} \tag {9b}
$$

where $\tilde { h } _ { i } \ > \ 0 , \ \forall i \in \mathbb { N } _ { 1 } ^ { r }$ and is updated based on the time-average of past state constraint violations in closed-loop. Note that violations of state constraints $( \mathrm { i . e . , } G x ( t ) > g )$ can occur in closed-loop, as the uncertainties come into effect. The adaptive constraint tightening in (9b) attempts to reduce the conservatism inherent to (9a) by incorporating past state constraint violation behavior of the system in closed-loop, but can still be over-conservative (see Remark 1).

Remark 1 (Over-Conservativeness of Previous Approaches): Equation (9a) approximates (4) conservatively [4], [6, Sec. II-A], as (9a) requires the constraint satisfaction conditionally on x(t) (i.e., for x(t) that can be reached at time t by the given control policy under the uncertainty sequence). Equation (4), however, requires constraint satisfaction in a more relaxed average sense (i.e., over all realizations of the uncertainty sequence up to t). Moreover, (9a) does not consider the memory of past state constraint violations which is critical in the present time-average reinterpretation of chance constraints. Incorporating past constraint violations by using the adaptive tightening in (9b) can still be conservative in satisfying (4) in closed-loop due to the over-estimation of the tightening parameter h˜ [4]. In addition, in (9b), the nominal MPC solutions never violate the state constraints over the prediction horizon, as a result of restricting the size of the feasible state set (despite a larger feasible state set being available to the controller as compared with the nominal MPC when accommodating for uncertainty), which the MPC optimizer can theoretically exploit to further reduce conservativeness in closed-loop.

Based on Remark 1, which shows that both (9a) and (9b) can be over-conservative in satisfying (4) in closed-loop, we propose to adaptively relax the state constraints in the nominal MPC instead of tightening them. The adaptive relaxation allows for state constraint violations over the nominal MPC prediction horizon $( G x ( t + k | t ) > g$ with $\mathbf { w } ( t ) = \mathbf { 0 } )$ , to push the system toward reduced conservativeness. We relax the satisfaction of (9a) and approximate (4) by reformulating the nominal state constraints as follows:

$$
G x (t + k | t) \leq g - h (t) \quad \forall k \in \mathbb {N} _ {1} ^ {N} \forall t \mathbf {w} (t) = \mathbf {0} \tag {10}
$$

where $h \in \mathbb { R } ^ { r }$ is the adaptive relaxing parameter with $h _ { i } < 0 ,$ , $\forall i \in \mathbb { N } _ { 1 } ^ { r }$ . It should be noted that the sign of $h _ { i }$ in (10) is opposite to $\tilde { h } _ { i }$ in (9b). We also observe that decreasing h(t) in (10) expands the feasible state set, pushing the system more toward state constraint violations $( \mathrm { i . e . , } G x ( t + k | t ) > g )$ , while increasing h(t) contracts the feasible state set pulling the system away from state constraint violations.3 The initial value of h at t = 0 can be calculated based on domain knowledge [18], which obviates the requirement of past uncertainty samples, as in [3]. The initial value of h is not important since h gets adapted as the system evolves with time [7]. The ordered collection of adaptive relaxation parameters along the MPC prediction horizon is denoted as follows:

$$
\mathbf {h} (t) := \left(h (t), h (t), \dots , h (t)\right) ^ {\top} \in \mathbb {R} ^ {N r}.
$$

3Note that in economic MG dispatch with BESS in VRE grids, where the objective function is the actual economic cost of system operation like the electricity bill and not necessarily only a penalty on the control input (BESS dispatch), relaxing the state constraints in the nominal MPC does not automatically lead the system to predicted nominal solutions that violate the (original) state constraints pathologically over the nominal MPC prediction horizon. The state (BESS SOC), in these applications, tries to exploit the full feasible state set to best reduce economic cost for the MPC prediction horizon. Nevertheless, the case where the proposed formulation can result in pathological constraint violations is averted in closed-loop by a postprocessing step described later in Section III-F and Remark 7.

The nominal OA-SMPC, which is assumed to be a convex optimization problem is then formulated as follows:

$$
\mathbf {u} ^ {*} (t) = \underset {\mathbf {u} (t) \in \mathbb {R} ^ {N m}} {\arg \min} J (t, x (t), \mathbf {u} (t), \mathbf {w} (t) = \mathbf {0}) \tag {11a}
$$

$$
\text { s.t. } \mathbf {x} (t + 1) = \mathbf {A} x (t) + \mathbf {B} \mathbf {u} (t) \tag {11b}
$$

$$
\mathbf {S u} (t) \leq \mathbf {s} \tag {11c}
$$

$$
\mathbf {M} \mathbf {u} (t) = \mathbf {c} (t) \tag {11d}
$$

$$
\mathbf {G} \mathbf {x} (t + 1) \leq \mathbf {g} - \mathbf {h} (t) \tag {11e}
$$

where $J \colon  { \mathbb { N } } \times  { \mathbb { R } } ^ { n } \times  { \mathbb { R } } ^ { N m } \times  { \mathbb { R } } ^ { N p } \to  { \mathbb { R } }$ is an arbitrary convex function, S ∈ RNq×Nm, $\mathbf { S } \in \mathbb { R } ^ { N q \times N m } , \ \mathbf { s } \in \mathbb { R } ^ { N q }$ , M $\in \mathbb { R } ^ { N d \times N m } , \dot { \mathbf { c } } \in \mathbb { R } ^ { N d }$ , $\mathbf { G } \in \mathbb { R } ^ { N r \times N n }$ , and $\mathbf { g } \in \mathbb { R } ^ { N r }$ . Note that dropping (11d) makes the problem setup similar to [3]. Note that in (11e), the MPC state constraints are applied for $x ( t + k | t )$ , $\forall k \in \mathbb { N } _ { 1 } ^ { N }$ , and not for the present state corresponding to $k = 0$ to allow for the present state to be outside of the feasible state set of the nominal OA-SMPC. Assumption 3, discussed next, ensures recursive feasibility and existence of an ideal control policy (described later in Assumption 5).

Assumption 3 (Control Inputs [3]): (a) The control input constraints (11c) are such that the system can provide enough control input to bring the predicted state at the next timestep, from any present state x(t), to the feasible region of the nominal OA-SMPC (11e). Specifically,

$$
\exists u (t | t) \text {   s.t   } S u (t | t) \leq s, \quad M u (t | t) = c (t | t)
$$

$$
G (x (t + 1 | t)) \leq g - h (t)
$$

where $x ( t + 1 | t ) = A x ( t ) + B u ( t | t )$ , for all t . (b) The system is one step controllable.

The condition for testing Assumption 3(a) which ensures recursive feasibility (similar to [3] and [30]) of the nominal OA-SMPC is given in the Appendix, and is excluded here for brevity. Assumption 3(a) ensures that after handling the uncertainty from the previous time step in closed-loop, resulting in the present state x(t), which may be outside the feasible state set of the nominal OA-SMPC (11e), the computed control input is strong enough to bring the predicted system state at the next time-step back to the feasible state set.

Assumption 3(a), while theoretically can be, is generally not restrictive for practical applications such as MGs or HVAC systems, as these systems are generally designed to be able to have enough control input power to be able to handle uncertainties [3]. Assumption 3(b) is more restrictive and is only used for ensuring sufficient conditions for the existence of an ideal control policy at every time step (see Assumption 5). Assumption 3(b) can be relaxed for practical applications such as the one described in the case study in Section IV.

Remark 2 (Structure of the Input Matrix): Note that Assumption 3(b) is sufficient for saying that the system has at least as many control inputs as states $( \mathrm { i } . \mathrm { e } . , n \le m )$ and B has full row rank. The assumption implies that if $n = m$ , B has an inverse, while if $n < m ,$ B has a right inverse.

Assumption 4 (Form of the h Update Rule): The online h (adaptive relaxing parameter) update rule can be written as $h _ { i } ( t ) : = h _ { i } ( t - 1 ) [ 1 + K _ { i } ( t ) ]$ , where $K _ { i } ( t ) > - 1$ , ∀t ensures $h _ { i } ( t ) < 0 .$ , ∀t [18, Eq. (12)].

Remark 3 (Behavior of the h Update Rule): In Assumption 4, $\begin{array} { r l r } { K _ { i } ( t ) } & { { } > } & { 0 } \end{array}$ decreases $h _ { i } ( t )$ , expanding the state constraints, pushing the system more toward state constraint violations, while $\begin{array} { r l r } { K _ { i } ( t ) } & { { } < } & { 0 } \end{array}$ increases $h _ { i } ( t )$ , contracting the state constraints, pulling the system away from state constraint violations.

## C. Observed Violations

In this section, for consistency with earlier works like $[ 3 ] ,$ we drop (3) and (11d). Thus, the nominal OA-SMPC computed optimal control inputs for the first time step of the prediction horizon are implemented in a closed-loop. The observed states get corrected once the uncertainties are realized, by using (1). The case where (3) and (11d) are considered in the problem formulation is discussed in Section III-F which additionally postprocesses the nominal OA-SMPC computed control inputs to correct for the uncertainty.

Without loss of generality, consider the ith state constraint in (4). Let $V _ { i } ( t + 1 ) \in \{ 0 , 1 \}$ track whether the state constraint is violated in closed-loop at time $t + 1$ , while $Y _ { i } ( t + 1 ) \in [ 0 , 1 ]$ keeps track of the time average of violations up to time $t + 1$ . Note that the control input applied at time t (along with the uncertainty realized at t) is manifested with updated system states, which can be observed only at $t + 1 , \mathrm { i . e . }$ , there is one time-step delay in observing violations (or nonviolations) from the time when the control inputs and uncertainties are applied

$$
V _ {i} (t + 1) := \left\{ \begin{array}{l l} 1, & G _ {i} (A x (t) + B u ^ {*} (t | t) + E w (t)) > g _ {i} \\ 0, & G _ {i} (A x (t) + B u ^ {*} (t | t) + E w (t)) \leq g _ {i} \end{array} \right. \tag {12a}
$$

$$
Y _ {i} (t + 1) := \frac {\sum_ {j = 1} ^ {t + 1} V _ {i} (j)}{t + 1}. \tag {12b}
$$

The framework for tracking the state constraint violations and time average of violations in the case of JCC, is the same as that of the individual chance constraints described in (12). The only difference in the case of JCC is that a violation occurs if any one of the constraints (involved in the JCC) violates its specific state constraint bounds in closed-loop.

## D. Convergence Properties of Y (t)

In this section, like Section III-C, without loss of generality, we limit our discussion to the i th state constraint in (4) with $i \in \mathbb { N } _ { 1 } ^ { r }$ , with its corresponding adaptive relaxation parameter $h _ { i } \in \mathbb { R } _ { 0 - }$ . The conditions established for ensuring the convergence of the time average of state constraint violations to the maximum allowable violation probability in this article use a similar simplifying assumption as in [3]. The simplifying assumption (see Assumption 5) allows the controller to apply ideal control inputs at the current time step leading to a desired probability of violation of state constraints at the next time step, under unknown bounded uncertainties.

Denoted by $Z _ { i } ( t ) ~ = ~ | \alpha _ { i } - Y _ { i } ( t ) |$ the absolute difference between the maximum allowable violation probability and time average of violations of the ith state constraint observed at time t. We have to ensure that $Y _ { i }$ tends to $\alpha _ { i }$ in closed-loop as the system evolves with time for nonconservative chance constraint satisfaction. The nonconservative strategy ideally leads to lower costs without violating the state constraints beyond the maximum allowable violation probability.

As $Z _ { i } ( t )$ is nonnegative, the convergence of $Z _ { i }$ can be guaranteed a.s., if $Z _ { i } ( t )$ is a supermartingale [28]. Following Section II-B, the three conditions for $Z _ { i }$ being a supermartingale are investigated as follows.

1) Let $\mathcal { F } _ { t } = \sigma ( \{ w ( 0 ) , w ( 1 ) , \ldots , w ( t - 1 ) \} )$ be a σ -algebra on uncertainties realized up to time $t - 1 . \ Z _ { i } ( t )$ depends on $Y _ { i } ( t )$ , which depends on the realization of all the uncertainties up to time $t - 1$ . Since $Z _ { i } ( t )$ is exactly known with information available up to time $t - 1 , Z _ { i } ( t )$ is $\mathcal { F } _ { t }$ measurable $\forall t > 0$ , and is thus adapted. Also, since $Z _ { i } ( t + 1 )$ is random with information available in $\mathcal { F } _ { t } .$ , the process $Z _ { i }$ is stochastic.  
2) As $V _ { i } ( t ) \in \{ 0 , 1 \} , Y _ { i } ( t ) \in [ 0 , 1 ]$ , and $\alpha _ { i } \in ( 0 , 0 . 5 )$ , thus $Z _ { i } ( t ) = | \alpha _ { i } - Y _ { i } ( t ) | \in [ 0 , 1 )$ . Thus, $\mathbb { E } [ | Z ( t ) | ] < 1 < \infty$ , $\forall t > 0 .$  
3) It remains to show $\mathbb { E } [ Z _ { i } ( t + 1 ) | \mathcal { F } _ { t } ] \le Z _ { i } ( t )$ a.s., $\forall t > 0 .$ , which we will show to hold under similar simplifying assumptions as in [3]. The assumption involves replacing the stochastic $Z _ { i } ( t + 1 ) | \mathcal { F } _ { t }$ by its ideal surrogate $Z _ { i } ^ { * } ( t +$ $1 ) | \mathcal { F } _ { t }$ as explained next.

From (12b), $Y _ { i } ( t + 1 )$ can be written as follows:

$$
Y _ {i} (t + 1) = \sum_ {j = 1} ^ {t + 1} \frac {V _ {i} (j)}{t + 1} = t \frac {Y _ {i} (t)}{t + 1} + \frac {V _ {i} (t + 1)}{t + 1}. \tag {13}
$$

Denoting $\mathbb { E } [ Z _ { i } ( t { + } 1 ) | \mathcal { F } _ { t } ] { - } Z _ { i } ( t )$ by $\Delta _ { i } ( t )$ and substituting (13) in $\Delta _ { i } ( t )$ results in

$$
\begin{array}{l} \Delta_ {i} (t) = \mathbb {E} \bigg [ \left| \alpha_ {i} - t \frac {Y _ {i} (t)}{t + 1} - \frac {V _ {i} (t + 1)}{t + 1} \right| | \mathcal {F} _ {t} \bigg ] \\ - \left| \alpha_ {i} - Y _ {i} (t) \right|. \tag {14} \\ \end{array}
$$

Let $p _ { i } ( t + 1 ) : = \mathbb { P } ( V _ { i } ( t + 1 ) = 1 | \mathcal { F } _ { t } )$ , which means that $p _ { i } ( t + 1 )$ is the probability of observing a constraint violation at time t + 1. Similarly, $1 - p _ { i } ( t + 1 ) = \mathbb { P } ( V _ { i } ( t + 1 ) = 0 | \mathcal { F } _ { t } )$ is the probability of not observing a violation at time t + 1. We notice that the only stochastic part within the expectation in the RHS of (14) is $V _ { i } ( t + 1 )$ based on $\mathcal { F } _ { t }$ . Therefore, (14) can be rewritten in terms of $p _ { i } ( t + 1 )$ to replace the expectation as follows:

$$
\begin{array}{l} \Delta_ {i} (t) = p _ {i} (t + 1) \left[ \left| \alpha_ {i} - t \frac {Y _ {i} (t)}{t + 1} - \frac {1}{t + 1} \right| \right] \\ + \left(1 - p _ {i} (t + 1)\right) \left[ \left| \alpha_ {i} - t \frac {Y _ {i} (t)}{t + 1} \right| \right] - | \alpha_ {i} - Y _ {i} (t) |. \tag {15} \\ \end{array}
$$

Simplifying yields

$$
\Delta_ {i} (t) = p _ {i} (t + 1) \beta_ {i} (t) + \left[ \left| \alpha_ {i} - t \frac {Y _ {i} (t)}{t + 1} \right| - | \alpha_ {i} - Y _ {i} (t) | \right] \tag {16}
$$

$$
\beta_ {i} (t) = \left| \alpha_ {i} - t \frac {Y _ {i} (t)}{t + 1} - \frac {1}{t + 1} \right| - \left| \alpha_ {i} - t \frac {Y _ {i} (t)}{t + 1} \right|. \tag {17}
$$

To keep the analysis applicable to any arbitrary probability distribution of w(t), we consider the sign of $\beta _ { i } ( t )$ to decide the ideal control policy [3], for which we introduce Assumption 5.

Assumption 5 (Ideal Control Policy): (a) There exists an ideal control policy (or ideal control input) at time t , applying which makes $p _ { i } ( t + 1 ) \ = \ p _ { i } ^ { * } ( t + 1 )$ , where $p _ { i } ^ { * } ( t + 1 )$ ∈ {0, 1}, ∀t . (b) When $\beta _ { i } ( t ) < 0$ , we apply ideal control inputs at t leading to $p _ { i } ^ { * } ( t + 1 ) = 1$ whereas, if $\beta _ { i } ( t ) > 0$ , we apply ideal control inputs at t leading to $p _ { i } ^ { * } ( t + 1 ) = 0$ .

Definition 6 (Ideal Surrogate of a Variable): The manifestation of a variable under the ideal control policy in Assumption 5 is defined as the ideal surrogate of that variable. It is denoted by an asterisk after the variable.

Assumption 5(a) ensures that there exists a control input which causes a constraint violation a.s. at the next time step $t + 1$ in closed-loop from any present state $x ( t ) , \ \mathrm { i . e . }$ , $p _ { i } ^ { * } ( t + 1 ) \ = \ 1$ . Similarly, $p _ { i } ^ { * } ( t + 1 ) \ = \ 0$ means that the controller can drive the system to prevent a constraint violation a.s. at $t + 1$ in closed-loop. $Z _ { i } ( t )$ and $\Delta _ { i } ( t )$ under the ideal control policy are referred to as $Z _ { i } ^ { * } ( t )$ and $\Delta _ { i } ^ { * } ( t )$ , respectively,4 consistent with Definition 6. Assumption 5(b) ensures that when $\beta _ { i } ( t ) \ < \ 0$ , we choose ideal control inputs leading to $p _ { i } ^ { * } ( t + 1 ) = 1$ so that $\Delta _ { i } ^ { * } ( t )$ may be $\leq 0 \ \mathrm { a . s }$ . Similarly, when $\beta _ { i } ( t ) ~ > ~ 0$ , Assumption 5(b) ensures that we choose ideal control inputs leading to $p _ { i } ^ { * } ( t + 1 ) = 0$ so that $\Delta _ { i } ^ { * } ( t )$ may be $\leq 0 \ \mathrm { a . s }$ . Note that despite the application of ideal control inputs, the second and third term in the RHS of (16) can lead to $\Delta _ { i } ^ { * } ( t ) > 0 .$ , which is used to derive the critical region $\kappa ( \alpha _ { i } , t )$ , in Theorem 1 later. The critical region signifies a region where if $Y _ { i } ( t ) \in \kappa ( \alpha _ { i } , t )$ , then $\Delta _ { i } ^ { * } ( t ) > 0 \ \mathrm { a . s . }$ .

The case when $\beta _ { i } ( t ) = 0$ implies $p _ { i } ^ { * } ( t + 1 )$ not having an effect on $\Delta _ { i } ( t )$ , as the first term in the RHS of (16) vanishes regardless of the value of $p _ { i } ( t + 1 )$ . From (17), it can be shown that βi (t) = 0 ⇔ Yi (t) = $\begin{array} { r } { \beta _ { i } ( t ) ~ = ~ 0 ~ \Leftrightarrow ~ Y _ { i } ( t ) ~ = ~ \frac { \alpha _ { i } - \frac { 1 } { 2 ( t + 1 ) } } { 1 - \frac { 1 } { ( t + 1 ) } } } \end{array}$ αi − 2(t+1) and further (t +1) solving for $\Delta _ { i } ( t ) ~ > ~ 0 ~ \mathrm { i n } ~ ( 1 6 )$ , yields $\begin{array} { r } { \alpha _ { i } { \mathrm { ~ \scriptsize ~ > ~ } } \frac { 1 } { 2 ( t + 1 ) } , } \end{array}$ 2(t+1) , which 1 becomes more likely to be satisfied as t increases. However, the case $\beta _ { i } ( t ) = 0$ can be avoided by a particular choice of $\alpha _ { i }$ . From (17)

$$
\beta_ {i} (t) \neq 0 \Leftrightarrow Y _ {i} (t) \neq \frac {\alpha_ {i} - \frac {1}{2 (t + 1)}}{1 - \frac {1}{(t + 1)}}.
$$

Since, $\begin{array} { r } { Y _ { i } ( t ) = ( \sum _ { j = 1 } ^ { t } V _ { i } ( j ) / t ) } \end{array}$ , and $\textstyle \sum _ { j = 1 } ^ { t } V _ { i } ( j ) \in \mathbb { N }$ , therefore,

$$
Y _ {i} (t) \neq \frac {\alpha_ {i} - \frac {1}{2 (t + 1)}}{1 - \frac {1}{(t + 1)}} \iff \sum_ {j = 1} ^ {t} V _ {i} (j) \neq (t + 1) \alpha_ {i} - \frac {1}{2}
$$

which can be ensured by appropriate choice of $\alpha _ { i }$ . Specifically, $( t + 1 ) \alpha _ { i } - ( 1 / 2 ) \not \in \mathbb { N }$ , ∀t. The online adaptive relaxation rule is introduced in Section III-D1, with its behavior under practical scenarios being discussed in Section III-D2.

1) h Update Rule: $\beta _ { i } ( t ) < 0$ implies $\begin{array} { r } { \alpha _ { i } - Y _ { i } ( t ) + \frac { 2 Y _ { i } ( t ) - 1 } { 2 ( t + 1 ) } > } \end{array}$ $0 , ^ { 5 }$ and is associated with violation of state constraints at $t +$ 1 which is achieved by expanding the state constraint limits in (11e). Similarly $\beta _ { i } ( t ) > 0$ implies $\begin{array} { r } { \alpha _ { i } - Y _ { i } ( t ) + \frac { 2 Y _ { i } ( t ) - 1 } { 2 ( t + 1 ) } < } \end{array}$ 0 and is associated with nonviolation of state constraints at

$^ 4 Z _ { i } ^ { * } ( t ) = | \alpha _ { i } - Y _ { i } ^ { * } ( t ) |$ and $\Delta _ { i } ^ { * } ( t ) = Z _ { i } ^ { * } ( t + 1 ) - Z _ { i } ( t )$ .

$\begin{array} { r } { ^ { 5 } \beta _ { i } ( t ) = | \zeta _ { i } ( t ) - \frac { 1 } { t + 1 } | - | \zeta _ { i } ( t ) | } \end{array}$ , where $\begin{array} { r } { \zeta _ { i } ( t ) = \alpha _ { i } - t \frac { Y _ { i } ( t ) } { t + 1 } } \end{array}$ . Solving for $\beta _ { i } ( t ) < 0$ leads to $\begin{array} { r } { \zeta _ { i } ( t ) > \frac { 1 } { 2 ( t + 1 ) } } \end{array}$ , which implies $\begin{array} { r } { \alpha _ { i } - Y _ { i } ( t ) + \frac { \dot { 2 } \dot { Y } _ { i } ( t ) - 1 } { 2 ( t + 1 ) } > 0 . } \end{array}$ 2Yi (t )−1

t + 1 which is achieved by contracting the limits in (11e). From Assumption 4, the $h _ { i }$ update rule can be framed with $\begin{array} { r } { K _ { i } ( t ) \propto \left[ \alpha _ { i } - Y _ { i } ( t ) + \frac { 2 Y _ { i } ( t ) - 1 } { 2 ( t + 1 ) } \right] } \end{array}$ as follows:

$$
h _ {i} (t) = h _ {i} (t - 1) \left[ 1 + \frac {\alpha_ {i} - Y _ {i} (t) + \frac {2 Y _ {i} (t) - 1}{2 (t + 1)}}{\gamma_ {i}} \right] \tag {18}
$$

where $\gamma _ { i } \in \mathbb { R } _ { 0 + }$ + is a constant of proportionality that adjusts the rate of $h _ { i }$ update ensuring $\frac { \alpha _ { i } - \bar { Y } _ { i } ( t ) + \frac { - 2 Y _ { i } ( t ) - 1 } { 2 ( t + 1 ) } } { \nu _ { i } } > - 1$ , ∀t . Note γi that Theorem 1, discussed next, implicitly assumes that (18) is able to enforce Assumption 5. However, it may be possible to devise other h update rules enforcing Assumption 5, wherein Assumption 4 [and consequently, use of (18)] can be relaxed. In practical applications, while (18) cannot guarantee satisfaction of Assumption 5 in closed-loop, it still encourages it (see Section III-D2).

Theorem 1: Let Assumptions 1–3 and 5 hold. Given $\alpha _ { i } \ >$ 12(t +1) , Z ∗i (t ), which is the ideal surrogate of the desired ${ \frac { 1 } { 2 ( t _ { 0 } + 1 ) } } , \ Z _ { i } ^ { * } ( t )$ supermartingale $Z _ { i } ( t )$ , is monotonically decreasing a.s. $\forall t \geq$ $t _ { 0 } ,$ if and only if, $Y _ { i } ( t ) \notin \kappa ( \alpha _ { i } , t ) , \forall t \geq t _ { 0 } .$ , where $\kappa ( \alpha _ { i } , t )$ is a neighborhood of $\alpha _ { i }$ defined as follows:

$$
\kappa (\alpha_ {i}, t) := \left(\frac {\alpha_ {i} - \frac {1}{2 (t + 1)}}{1 - \frac {1}{2 (t + 1)}}, \frac {\alpha_ {i}}{1 - \frac {1}{2 (t + 1)}}\right).
$$

Proof: Theorem 1 says that given $\alpha _ { i } > ( 1 / ( 2 ( t _ { 0 } + 1 ) ) )$ , $\mathcal { P } \iff \mathcal { Q }$ , where $\mathcal { P }$ is defined as $\Delta _ { i } ^ { * } ( t ) \leq 0 \mathrm { ~ a . s . , } \forall t \geq t _ { 0 } .$ , and $\mathcal { Q }$ is defined as $Y _ { i } ( t ) \notin \kappa ( \alpha _ { i } , t ) , \forall t \ \geq \ t _ { 0 } .$ . To prove, $\mathcal { P } \quad \Longleftrightarrow \quad \mathcal { Q } .$ , we will first prove the inverse as true, i.e., $\lnot \mathcal { P } \implies \lnot \mathcal { Q }$ , which implies $\mathcal { Q } \implies \mathcal { P }$ . Then we will prove the contrapositive as true, $\mathrm { i . e . , } \ \lnot \mathcal { Q } \ \Longrightarrow \ \lnot \mathcal { P }$ , which implies $\mathcal { P } \implies \mathcal { Q }$ , which will complete the proof.

To prove $\lnot \mathcal { P } \implies \lnot \mathcal { Q }$ , assume $\neg \mathcal { P }$ is true, i.e., $\Delta _ { i } ^ { * } ( t ) >$ 0 a.s. To show, $\neg \mathcal { Q }$ holds, we consider Cases 1–3 based on the sign of $\beta _ { i } ( t )$ given as follows.

Case 1:

$$
\beta_ {i} (t) <   0 \Leftrightarrow p _ {i} ^ {*} (t + 1) = 1
$$

$$
\Leftrightarrow \alpha_ {i} - Y _ {i} (t) + \frac {2 Y _ {i} (t) - 1}{2 (t + 1)} > 0
$$

$$
\Leftrightarrow Y _ {i} (t) <   \frac {\alpha_ {i} - \frac {1}{2 (t + 1)}}{1 - \frac {1}{t + 1}}. \tag {19a}
$$

Solving for $\Delta _ { i } ^ { * } ( t ) > 0$ when $p _ { i } ^ { * } ( t + 1 ) = 1$ yields

$$
Y _ {i} (t) > \frac {\alpha_ {i} - \frac {1}{2 (t + 1)}}{1 - \frac {1}{2 (t + 1)}}. \tag {19b}
$$

Equation (19) implies the critical region of Case 1 is $\begin{array} { r } { \kappa _ { 1 } ( \alpha _ { i } , t ) = \bigg ( \frac { \alpha _ { i } - \frac { 1 } { 2 ( t + 1 ) } } { 1 - \frac { 1 } { 2 ( t + 1 ) } } , \frac { \alpha _ { i } - \frac { 1 } { 2 ( t + 1 ) } } { 1 - \frac { 1 } { t + 1 } } \bigg ) } \end{array}$ 2(t +1) αi − 2(t+1) implying if $Y _ { i } ( t ) \in \kappa _ { 1 } ( \alpha _ { i } , t )$ , 1− t +1 despite applying ideal control inputs to the system at t leading to $p _ { i } ^ { * } ( t + 1 ) = 1 , \Delta _ { i } ^ { * } ( t ) > 0 \ \mathrm { a . s }$ .

Case 2:

$$
\beta_ {i} (t) > 0 \Leftrightarrow p _ {i} ^ {*} (t + 1) = 0
$$

$$
\Leftrightarrow \alpha_ {i} - Y _ {i} (t) + \frac {2 Y _ {i} (t) - 1}{2 (t + 1)} <   0
$$

$$
\Leftrightarrow Y _ {i} (t) > \frac {\alpha_ {i} - \frac {1}{2 (t + 1)}}{1 - \frac {1}{t + 1}}. \tag {20a}
$$

Solving for $\Delta _ { i } ^ { * } ( t ) > 0$ when $p _ { i } ^ { * } ( t + 1 ) = 0$ yields

$$
Y _ {i} (t) <   \frac {\alpha_ {i}}{1 - \frac {1}{2 (t + 1)}}. \tag {20b}
$$

Equation (20) implies the critical region of Case 2 is $\begin{array} { r } { \kappa _ { 2 } ( \alpha _ { i } , t ) = \left( \frac { \alpha _ { i } - \frac { 1 } { 2 ( t + 1 ) } } { 1 - \frac { 1 } { t + 1 } } , \frac { \alpha _ { i } } { 1 - \frac { 1 } { 2 ( t + 1 ) } } \right) } \end{array}$ αi − 12(t+1) implying if $Y _ { i } ( t ) \in \kappa _ { 2 } ( \alpha _ { i } , t )$ , despite applying ideal control inputs to the system at t leading to $p _ { i } ^ { * } ( t + 1 ) = 0 , \Delta _ { i } ^ { * } ( t ) > 0$ a.s.

Case 3: $\beta _ { i } ( t ) = 0$ leads to $\Delta _ { i } ^ { * } ( t ) > 0$ a.s. because $\alpha _ { i } \ >$ $\begin{array} { r } { \frac { 1 } { 2 ( t _ { 0 } + 1 ) } \ge \frac { 1 } { 2 ( t + 1 ) } , \forall t \ge t _ { 0 } } \end{array}$ 12(t+1) , ∀t ≥ t0. In addition, βi (t ) = 0 ⇔ Yi (t ) = $\beta _ { i } ( t ) = 0 \Leftrightarrow Y _ { i } ( t ) =$ $\frac { \alpha _ { i } - \frac { \imath } { 2 ( t + 1 ) } } { 1 - \frac { 1 } { ( t + 1 ) } } = \kappa _ { 3 } ( \alpha _ { i } , t )$ αi − 2(t+1) . 1− 1+

The total critical region from Cases 1–3 yields

$$
\begin{array}{l} \kappa (\alpha_ {i}, t) = \kappa_ {1} (\alpha_ {i}, t) \cup \kappa_ {2} (\alpha_ {i}, t) \cup \kappa_ {3} (\alpha_ {i}, t) \\ = \left(\frac {\alpha_ {i} - \frac {1}{2 (t + 1)}}{1 - \frac {1}{2 (t + 1)}}, \frac {\alpha_ {i}}{1 - \frac {1}{2 (t + 1)}}\right) \\ \end{array}
$$

which shows that if $\Delta _ { i } ^ { * } ( t ) ~ > ~ 0 ~ \mathrm { a . s . }$ ., then $Y _ { i } ( t ) \ \in \ \kappa ( \alpha _ { i } , t )$ , which proves $\lnot \mathcal { P } \implies \lnot \mathcal { Q }$ .

To prove $\lnot \mathcal { Q } \implies \lnot \mathcal { P }$ , assume, ¬Q, i.e., Yi (t ) ∈ κ (αi , t ). $\neg \mathcal { Q }$ is subdivided into Cases 4–6.

Case 4: $\beta _ { i } ( t ) < 0 \Leftrightarrow p _ { i } ^ { * } ( t + 1 ) = 1$ , which yields (19a). Equation (19a) and $\begin{array} { r l r } { Y _ { i } ( t ) } & { { } \in } & { \kappa ( \alpha _ { i } , t ) } \end{array}$ yields, $\begin{array} { r l } { Y _ { i } ( t ) } & { { } \in } \end{array}$ $\begin{array} { r } { \left( \frac { \alpha _ { i } - \frac { 1 } { 2 ( t + 1 ) } } { 1 - \frac { 1 } { 2 ( t + 1 ) } } , \frac { \alpha _ { i } - \frac { 1 } { 2 ( t + 1 ) } } { 1 - \frac { 1 } { t + 1 } } \right) = \kappa _ { 1 } ( \alpha _ { i } , t ) . } \end{array}$ αi − 12(t +1) αi − 2(t+1) 1− 1

However, from Case 1, we know that when $p _ { i } ^ { * } ( t + 1 ) = 1$ and $Y _ { i } ( t ) \in \kappa _ { 1 } ( \alpha _ { i } , t )$ , then $\Delta _ { i } ^ { * } ( t ) > 0 \ \mathrm { a . s }$ .

Case $5 \colon \beta _ { i } ( t ) > 0 \Leftrightarrow p _ { i } ^ { * } ( t + 1 ) = 0$ , which yields (20a). Equation (20a) and $\begin{array} { r l r } { Y _ { i } ( t ) } & { { } \in } & { \kappa ( \alpha _ { i } , t ) } \end{array}$ yields, $\begin{array} { r l } { Y _ { i } ( t ) } & { { } \in } \end{array}$ $\begin{array} { r } { \left( \frac { \alpha _ { i } - \frac { 1 } { 2 ( t + 1 ) } } { 1 - \frac { 1 } { t + 1 } } , \frac { \alpha _ { i } } { 1 - \frac { 1 } { 2 ( t + 1 ) } } \right) = \kappa _ { 2 } ( \alpha _ { i } , t ) . } \end{array}$ αi − 12(t+1) 1− 1t+1

However, from Case 2, we know that when $p _ { i } ^ { * } ( t + 1 ) = 0$ and $Y _ { i } ( t ) \in \kappa _ { 2 } ( \alpha _ { i } , t )$ , then $\Delta _ { i } ^ { * } ( t ) > 0 \mathrm { a } . .$ s.

$C a s e \ 6 \colon \ \beta _ { i } ( t ) \ = \ 0 \ \Leftrightarrow \ Y _ { i } ( t ) \ = \ \kappa _ { 3 } ( \alpha _ { i } , t )$ which leads to $\Delta _ { i } ^ { * } ( t ) > 0 \ \mathrm { a . s }$ 1∗i (t ) > 0 a.s. because αi > 12(t+1) . $\begin{array} { r } { \alpha _ { i } > \frac { 1 } { 2 ( t + 1 ) } . } \end{array}$

Cases 4–6 together prove $\lnot \mathcal { Q } \stackrel { } { \Longrightarrow } - \mathcal { P }$ , which completes the proof.

Remark 4 (Extension of Theorem 1): Theorem 1

establishes the conditions necessary and sufficient for $\Delta _ { i } ^ { * } ( t ) ~ \leq ~ 0 ~ \mathrm { a . s . , } ~ \forall t ~ \geq ~ t _ { 0 } ,$ which implies that $Z _ { i } ^ { * } ( t )$ is monotonically decreasing a.s., $\begin{array} { r l } { \forall t } & { { } \ge \quad t _ { 0 } } \end{array}$ . From Cases 1 and 4, and 2, and $^ { 5 , }$ if the critical region is defined by $\begin{array} { r } { \kappa ^ { \prime } ( \alpha _ { i } , t ) : = \left\lceil \frac { \alpha _ { i } - \frac { 1 } { 2 ( t + 1 ) } } { 1 - \frac { 1 } { 2 ( t + 1 ) } } , \frac { \alpha _ { i } } { 1 - \frac { 1 } { 2 ( t + 1 ) } } \right\rceil } \end{array}$ αi − 12(t+1) then $\Delta _ { i } ^ { * } ( t ) ~ < ~ 0 ~ \mathrm { a . s . }$ , 1− 12(t+1) − 2(t +1) $\forall t \geq t _ { 0 }$ , which implies that $Z _ { i } ^ { * } ( t )$ is strictly decreasing $\begin{array} { r l r } { \mathrm { a . s . , ~ } \forall t } & { { } \geq } & { t _ { 0 } . } \end{array}$ . As $Z _ { i } ^ { * } ( t )$ is bounded from below by 0, we conclude lim $ \ Q _ { i } ^ { } ( t ) ~ = ~ \operatorname * { i n f } \{ Z _ { i } ^ { * } ( t ) \} ~ = ~ 0 ~ \mathrm { a . s } ^ { }$ . from the monotone convergence theorem, which implies the asymptotic convergence of $Y _ { i } ^ { * }$ to , if $\begin{array} { r } { \begin{array} { l r l } { \alpha _ { i } } & { > } & { \frac { 1 } { 2 ( t _ { 0 } + 1 ) } . } \end{array} } \end{array}$ 1 , $\begin{array} { r l } { \alpha _ { i } } & { { } \mathbf { a } . \mathbf { S } . } \end{array}$ $p _ { i } ( t + 1 ) = p _ { i } ^ { * } ( t + 1 )$ , and $Y _ { i } ( t ) \notin \kappa ^ { \prime } ( \alpha _ { i } , t ) , \forall t \geq t _ { 0 } .$

Remark 5 (Width of the Critical Region [3]): The width of the critical region $\kappa ^ { \prime } ( \alpha _ { i } , t ) \quad \mathrm { i s } \quad ( 1 / ( 2 t + 1 ) )$ ), while

6Note that although $Y _ { i } ( t _ { 0 } )$ need not necessarily be $Y _ { i } ^ { * } ( t _ { 0 } )$ , we abuse the notation to use $Z _ { i } ( t _ { 0 } )$ and $Z _ { i } ^ { * } ( t _ { 0 } )$ interchangeably in Theorem 1 and Remark 4 to aid in a more intuitive explanation.

$Y _ { i } ( t ) \ \in \ \{ 0 , ( 1 / t ) , ( 2 / t ) , \ldots , 1 \}$ . The granularity in possible values of $Y _ { i } ( t ) { \mathrm { ~ i s ~ } } 1 / t , \forall t . { \mathrm { ~ A s ~ } } { \frac { 1 } { 2 t + 1 } } < { \frac { 1 } { t } }$ , there is at most one critical value of $Y _ { i } ( t )$ , such that despite adapting the system to take ideal control inputs leading to $p _ { i } ( t + 1 ) = p _ { i } ^ { * } ( t + 1 )$ , it is possible that $\Delta _ { i } ^ { * } ( t ) \geq 0$ which can lead to the deviation of $Y _ { i } ^ { * }$ from $\alpha _ { i }$ momentarily. However, the width of the critical region monotonically decreases as t increases; therefore, the assumption of $Y _ { i } ( t ) \not \in \kappa ^ { \prime } ( \alpha _ { i } , t )$ , is weak and can be easily satisfied as t increases. The width of the critical region in the present work is also smaller than [3], implying more relaxed conditions for guaranteeing convergence of $Y _ { i } ^ { * }$ to $\alpha _ { i }$ .

Lemma 1: Let Assumptions 1–3 and 5 hold. Given any initial time $t _ { 0 } ,$ with $\begin{array} { r } { \alpha _ { i } > \frac { 1 } { 2 ( t _ { 0 } + 1 ) } } \end{array}$ and $Y _ { i } ( t _ { 0 } ) \notin \kappa ^ { \prime } ( \alpha _ { i } , t _ { 0 } )$ , there exists some time $t ^ { \prime } = \operatorname* { i n f } \{ t > t _ { 0 } \mid Y _ { i } ^ { * } ( t ) \in \kappa ^ { \prime } ( \alpha _ { i } , t ) \} \ \mathrm { a . s }$ .

Proof: The lemma states that if the initial time average of violations of state constraints is outside of the critical region, and we apply ideal control inputs from thereon, then at some future time $t ^ { \prime } ,$ , the time average of violations comes inside the critical region a.s. Lemma 1 implies the a.s. strict decrease of $Z _ { i } ^ { * } ( t )$ is violated for $t \geq t ^ { \prime }$ (see Remark 4). Lemma 1 can be proved by showing that when $Z _ { i } ^ { * } ( t )$ decreases, then $\begin{array} { r l r } { \frac { \delta \hat { w _ { i } } ( t ) } { \delta t } } & { { } > } & { \frac { \delta Z _ { i } ^ { * } ( t ) } { \delta t } } \end{array}$ where $\hat { w } ( t )$ is the width of the critical region at time t, and δ is the forward finite difference operator.

$\begin{array} { r } { \hat { w } _ { i } ( t ) : = \frac { 1 } { 2 t + 1 } } \end{array}$ , implying $\delta { \hat { w } } _ { i } ( t ) : = { \hat { w } } _ { i } ( t + 1 ) - { \hat { w } } _ { i } ( t ) =$ $\frac { - 2 } { ( 2 t + 1 ) ( 2 t + 3 ) }$ −2 where $\delta t : = 1$ . Thus, ${ \hat { w } } _ { i } ( t )$ decreases in the order of $\mathcal { O } ( 1 / t ^ { 2 } )$ .

Similarly, $\delta Z _ { i } ^ { * } ( t ) : = Z _ { i } ^ { * } ( t + 1 ) - Z _ { i } ( t ) = \Delta _ { i } ^ { * } ( t ) . \ \Delta _ { i } ^ { * } ( t )$ can be subdivided into Cases 1 and 2.

$\begin{array} { r l } & { \mathrm { ~ } C a s e ~ l \colon \mathrm { ~ W h e n ~ } p _ { i } ^ { * } ( t ~ + ~ 1 ) } \\ & { \left[ \left| \alpha _ { i } - t \frac { Y _ { i } ( t ) } { t + 1 } - \frac { 1 } { t + 1 } \right| \right] - | \alpha _ { i } - Y _ { i } ( t ) | . } \end{array}$ = 1, 1∗(t ) =

Case 2: When $\begin{array} { r } { \overline { { p } } _ { i } ^ { * } ( t + 1 ) = 0 , \Delta _ { i } ^ { * } ( t ) = \Big | \alpha _ { i } - t \frac { Y _ { i } ( t ) } { t + 1 } \Big | - | \alpha _ { i } - } \end{array}$ $Y _ { i } ( t ) |$ .

Substituting $\begin{array} { r } { Y _ { i } ( t ) = ( \sum _ { i = 1 } ^ { t } V _ { i } ( j ) / t ) } \end{array}$ , where $\begin{array} { r } { \sum _ { j = 1 } ^ { t } V _ { i } ( j ) \le } \end{array}$ t, in both Cases 1 and 2, we see that when $Z _ { i } ^ { * } ( i )$ decreases a.s. by virtue of $Y _ { i } ( t ) \not \in \kappa ^ { \prime } ( \alpha _ { i } , t )$ , then $Z _ { i } ^ { * } ( t )$ decreases most modestly (i.e., with the least magnitude of decrease), in the order of $\mathcal { O } ( 1 / t )$ , which proves that, $\begin{array} { r l r } { \frac { \delta \hat { w _ { i } } ( t ) } { \delta t } } & { { } > } & { \frac { \delta Z _ { i } ^ { * } ( t ) } { \delta t } } \end{array}$ which completes the proof.

Lemma 2: Let Assumptions 1–3 and 5 hold. Given any initial time $t _ { 0 } ,$ with $\begin{array} { r } { \alpha _ { i } > \frac { 1 } { 2 ( t _ { 0 } + 1 ) } } \end{array}$ and $Y _ { i } ( t _ { 0 } ) \in \kappa ^ { \prime } ( \alpha _ { i } , t _ { 0 } )$ , there exists some time $t ^ { \prime } = \operatorname* { i n f } \{ t > t _ { 0 } \mid Y _ { i } ^ { * } ( t ) \not \in \kappa ^ { \prime } ( \alpha _ { i } , t ) \} \ \mathrm { a . s }$ .

Proof: The lemma states that if the initial time average of violation of state constraints is inside the critical region, and we apply ideal control inputs from thereon, then at some future time $t ^ { \prime } ,$ , the time average of violation goes outside of the critical region a.s. Lemma $2$ prevents the monotonic increase of $Z _ { i } ^ { * } ( t )$ for $t \geq t ^ { \prime }$ . When $\begin{array} { r } { Y _ { i } ( t ) \in \kappa ^ { \prime } ( \alpha _ { i } , t ) , ~ \frac { \delta Z _ { i } ^ { * } ( t ) } { \delta t } \geq 0 . } \end{array}$ , while $\begin{array} { r } { \frac { \delta \hat { w } _ { i } ( t ) } { \delta t } < 0 } \end{array}$ δwˆi (t) < 0, thus leading to $\begin{array} { r } { \frac { \delta Z _ { i } ^ { * } ( t ) } { \delta t } > \frac { \delta \hat { w _ { i } } ( t ) } { \delta t } } \end{array}$ δwˆi (t) , which completes the proof. □

Theorem 2: Let Assumptions 1–3, and 5 hold. Given any $t _ { 0 }$ $\begin{array} { r } { \alpha _ { i } > \frac { 1 } { 2 ( t _ { 0 } + 1 ) } } \end{array}$ $Y _ { i } ( t _ { 0 } ) , Y _ { i } ^ { * }$ asymptotically converges to $\alpha _ { i } \ \mathrm { a . s . }$ .

Proof: The theorem states that if ideal control inputs are applied to the system starting from any arbitrary time $t _ { 0 }$ with $\begin{array} { r } { \alpha _ { i } ~ > ~ \frac { 1 } { 2 ( t _ { 0 } + 1 ) } ; } \end{array}$ 12(t +1) ; then, the time-average of violations converges to the maximum probability of violations of state constraints a.s. The proof follows from Theorem 1, Remarks 4, 5, and Lemmas 1 and 2, and relaxes the assumption of $Y _ { i } ( t ) \notin \kappa ^ { \prime } ( \alpha _ { i } , t ) , \forall t \ \geq \ t _ { 0 } .$ , of Remark 4. Theorem 2 also rigorously proves the result which was intuitively argued in [3].

From Remark 5, the width of the critical region $\hat { w } _ { i } ( t )$ is $( 1 / ( 2 t + 1 ) )$ , which monotonically decreases with time. $\mathbf { A } \mathbf { s }$ the critical region $\kappa ^ { \prime } ( \boldsymbol { \alpha } _ { i } , t )$ is a neighborhood of $\alpha _ { i }$ by construction, decrease of ${ \hat { w } } _ { i } ( t )$ implies a decrease of the width of the neighborhood. lim $ _ { t  \infty } \hat { w } _ { i } ( t ) = 0 .$ , which implies lim $_ { t  \infty } \kappa ^ { \prime } ( \alpha _ { i } , t ) = \alpha _ { i } , \mathrm { i . e . }$ , the critical region becomes a point. With αi > $\begin{array} { r } { \alpha _ { i } > \frac { 1 } { 2 ( t _ { 0 } + 1 ) } } \end{array}$ and $p _ { i } ( t + 1 ) = p _ { i } ^ { * } ( t + 1 )$ , $\forall t \geq t _ { 0 }$ , two cases are possible.

Case 1: When $Y _ { i } ( t _ { 0 } ) \notin \kappa ^ { \prime } ( \alpha _ { i } , t )$ , Lemma 1 concludes that there exists some $t ^ { \prime } = \operatorname* { i n f } \{ t > t _ { 0 } | Y _ { i } ^ { * } ( t ) \in \kappa ^ { \prime } ( \alpha _ { i } , t ) \} \ \mathrm { a . s } ,$ , which implies that $Y _ { i } ^ { * } ( t ^ { \prime } )$ is closer to $\alpha _ { i }$ as compared to $Y _ { i } ( t _ { 0 } )$ a.s. (see Remark 4). Then, from Lemma 2, we can conclude that there exists some $t ^ { \prime \prime } = \operatorname* { i n f } \{ t > t ^ { \prime } | Y _ { i } ^ { * } ( t ) \notin \kappa ^ { \prime } ( \alpha _ { i } , t ) \} \ \mathrm { a . s . }$ ., which implies that $Y _ { i } ^ { * } ( t ^ { \prime \prime } )$ is no closer to $\alpha _ { i }$ as compared with $Y _ { i } ^ { * } ( t ^ { \prime } )$ a.s. (see Remark 4). The process repeats, with $Y _ { i } ^ { * }$ coming in and out of the critical region a.s. as time progresses. However, as the width of the critical region vanishes at $t \to \infty$ , making the critical region the point $\alpha _ { i } .$ we conclude $Y _ { i } ^ { * }$ converges to $\alpha _ { i } \ { \mathrm { { a . s . } } }$ as $t \to \infty$ .

Case 2: When $Y _ { i } ( t _ { 0 } ) \in \kappa ^ { \prime } ( \alpha _ { i } , t )$ , a similar argument can be made as in Case 1 involving Lemmas 1 and 2, with $Y _ { i } ^ { * }$ coming in and out of the critical region a.s. until ultimately converging to $\alpha _ { i }$ a.s. as $t \to \infty$ .

Both Cases 1 and 2 complete the proof.

![](images/5fa249c36a27c741faf07c54f933288938c30a239c42e33c67eadbe46a5fb1f2.jpg)

2) Deviation of the Practical Control Policy From the Ideal Control Policy: Remark 5 establishes that the assumption of $Y _ { i } ( t ) \not \in \kappa ^ { \prime } ( \alpha _ { i } , t )$ , is weak and can be easily satisfied as t increases. Thus, given $Y _ { i } ( t ) \notin \kappa ^ { \prime } ( \alpha _ { i } , t )$ , if at some time $\begin{array} { r } { t , Y _ { i } ( t ) ~ < ~ \frac { \alpha _ { i } - \frac { 1 } { 2 ( t + 1 ) } } { 1 - \frac { 1 } { 2 ( t + 1 ) } } } \end{array}$ αi − 12(t+1) , then $\begin{array} { r } { Y _ { i } ( t ) ~ < ~ \frac { \alpha _ { i } - \frac { \imath } { 2 ( t + 1 ) } } { 1 - \frac { 1 } { ( t + 1 ) } } ~ < ~ \alpha _ { i } } \end{array}$ αi − 12(t+1) holds, which 1− 12(t+1) 1 (t +1) results in $h _ { i } ( \ddot { t } ) \overset { ^ { \prime } } { < } h _ { i } ( t - 1 )$ on applying (18). The decrease of $h _ { i }$ expands the feasible state set for $x ( t + 1 )$ in (11) and thereby encourages constraint violations at $t + 1$ in closedloop. While under the ideal control policy (Assumption 5), $h _ { i } ( t ) < h _ { i } ( t - 1 )$ would have guaranteed constraint violation at time $t + 1 \ \mathrm { a . s . }$ ., in practical scenarios, just the expansion of the feasible state set alone cannot guarantee violation. A similar argument can be made when $\begin{array} { r l r } { Y _ { i } ( t ) } & { > } & { \frac { \alpha _ { i } } { 1 - \frac { 1 } { 2 ( t + 1 ) } } , } \end{array}$

which by virtue of $\begin{array} { r } { Y _ { i } ( t ) > \alpha _ { i } > \frac { \alpha _ { i } - \frac { 1 } { 2 ( t + 1 ) } } { 1 - \frac { 1 } { ( t + 1 ) } } } \end{array}$ αi − 1+ and (18) leads to 1− $h _ { i } ( t ) > h _ { i } ( t - 1 )$ , contracting the feasible state set for $x ( t + 1 )$ thereby discouraging constraint violations at $t + 1$ in closedloop. Thus, under deviation of the practical control policy from the ideal, while asymptotic convergence of $Y _ { i }$ to $\alpha _ { i }$ cannot be guaranteed, it still is encouraged by (18).

## E. Asymptotic Behavior of the Practical System

It is important to determine the asymptotic behavior $( \mathrm { i . e . }$ , as $t  \infty )$ of the practical system in which the simplifying assumption of applying an ideal control policy referred to in Assumption 5 is dropped.

Theorem 3: Let Assumptions $1 - 3 ( \mathrm { a } )$ hold. The expected value of $Y _ { i } ( t + 1 ) | \mathcal { F } _ { t }$ asymptotically converges to $Y _ { i } ( t )$ .

Proof: Applying the limit of $t  \infty \mathrm { t o } ( 1 6 )$ , and rearranging to bypass the indeterminate $( \infty / \infty )$ form, we get

$$
\begin{array}{l} \lim _ {t \rightarrow \infty} \Delta_ {i} (t) = \lim _ {t \rightarrow \infty} p _ {i} (t + 1) \left[\left| \alpha_ {i} - \frac {Y _ {i} (t)}{1 + \frac {1}{t}} - \frac {1}{t + 1} \right|\right. \\ \left. - \left| \alpha_ {i} - \frac {Y _ {i} (t)}{1 + \frac {1}{t}} \right| \right] + \left| \alpha_ {i} - \frac {Y _ {i} (t)}{1 + \frac {1}{t}} \right| - | \alpha_ {i} - Y _ {i} (t) |. \tag {21} \\ \end{array}
$$

As $p _ { i } ( t + 1 ) \in [ 0 , 1 ]$ , substituting $t  \infty$ in (21), yields $\begin{array} { r } { \operatorname* { l i m } _ { t  \infty } \Delta _ { i } ( t ) = 0 . } \end{array}$ , which implies the following.

Case $\begin{array} { r } { I \colon \operatorname* { l i m } _ { t \to \infty } Y _ { i } ( t ) = \operatorname* { l i m } _ { t \to \infty } \mathbb { E } [ Y _ { i } ( t + 1 ) | \mathcal { F } _ { t } ] . } \end{array}$

Case 2: $\begin{array} { r } { \operatorname* { l i m } _ { t \to \infty } Y _ { i } ( t ) ~ = ~ \alpha _ { i } + l } \end{array}$ and $\begin{array} { r } { \operatorname* { l i m } _ { t \to \infty } \mathbb { E } [ Y _ { i } ( t \ + } \end{array}$ $1 ) | \mathcal { F } _ { t } ] = \alpha _ { i } - l ,$ where $l \in [ - \alpha _ { i } , \alpha _ { i } ] .$

Taking expectation on both sides of (13) yields

$$
\mathbb {E} [ Y _ {i} (t + 1) | \mathcal {F} _ {t} ] = t \frac {Y _ {i} (t)}{t + 1} + \mathbb {E} \bigg [ \frac {V _ {i} (t + 1) | \mathcal {F} _ {t}}{t + 1} \bigg ]. \tag {22}
$$

Taking the limit at $t \to \infty$ in (22), and substituting the values of $\scriptstyle \operatorname* { l i m } _ { t \to \infty } Y _ { i } ( t )$ and lim $_ { t  \infty } \mathbb { E } [ Y _ { i } ( t + 1 ) | \mathcal { F } _ { t } ]$ yields

$$
\alpha_ {i} - l = \lim _ {t \rightarrow \infty} \frac {\alpha_ {i} + l}{1 + \frac {1}{t}} + \lim _ {t \rightarrow \infty} \mathbb {E} \left[ \frac {V _ {i} (t + 1) \mid \mathcal {F} _ {t}}{t + 1} \right]. \tag {23}
$$

As $\mathbb { E } [ V _ { i } ( t + 1 ) | \mathcal { F } _ { t } ] = p _ { i } ( t + 1 ) \in [ 0 , 1 ] .$ , hence evaluating the limit in (23) yields $l = 0 .$ Hence Case 2 implies Case 1 (but not the other way around).

Case 1 completes the proof.

![](images/1399b691e76c71b4fc0bdb558466862acc340bc29c14bfc0026c18811f96c623.jpg)

The above proof shows that the time-average of the state constraint violations has a martingale-like behavior asymptotically which may be useful for practical operation of the proposed OA-SMPC to avoid unpredictable violation behavior in the long run as the system evolves.

## F. Postprocessing for Real-Time System Operation

In previous works such as [3], after the nominal MPC computed optimal control inputs for the first time-step of the prediction horizon are implemented, the observed states get corrected in closed-loop to account for real-time uncertainties by (1). Accommodation of the entire uncertainty (uncertainty in weather forecast) by the state is reasonable for building climate control applications where the state (room temperature) and control input (heating/cooling effect from the air conditioner) are not coupled to the same physical equipment [3]. However, in certain applications where the states and control inputs are coupled to the same equipment (like BESS, where the SOC is the state, and charging/discharging power is a control input), the first time-step optimal control inputs (computed by the nominal MPC) can also be postprocessed in real time to correct for the realized uncertainty. For example, the BESS can alter its nominal MPC computed optimal control inputs and states to correct for the VRES and gross load forecast uncertainty in real-time to maintain the power balance of the MG with the main grid.

Assumption 6 (Postprocessing to Correct for Uncertainties in Real-Time:) For each chance constrained state affected by uncertainties: (a) There are two mutually coupled sources of control with one source having the primary responsibility of handling the uncertainties in closed-loop. During correction of the observed states in closed-loop to account for the uncertainty, the feasible altered control input and state set is the same as defined by $\mathbf { s } _ { 1 : q }$ (11c) and $\mathbf { g } _ { 1 : r } - \mathbf { h } _ { 1 : r } ( t )$ (11e), respectively, which are the time-varying design limitations. The secondary control source always has enough control input available to handle the remaining part of the uncertainty [through satisfaction of (3)] that cannot be handled by the primary source due to the possibility of violation of the time-varying design limitations by the primary control source in closed-loop, (b) the state transition is not dependent on the secondary control source, (c) the primary and secondary control sources are coupled only to each other, and (d) the state transition is not dependent on the control sources associated with other states.

![](images/00f7f38d84eb39763369a083949da7b87eb7adfa6748ae4087e1b190d7fee79e.jpg)

<details>
<summary>flowchart</summary>

```mermaid
graph LR
  A["Forecast Model"] --> B["c(t)"]
  B --> C["OA-SMPC"]
  C --> D["x(t+1)\nu*(t)"]
  D --> E["Post-processing"]
  E --> F["u(t)"]
  F --> G["Plant"]
  G --> H["x(t+1)\nV(t+1)\ny(t+1)"]
  H --> I["Adaptive relaxing"]
  I --> J["h(t+1)\nx(t+1)"]
  J --> C
  E --> K["w(t)"]
  K --> E
    style E fill:#f9f,stroke:#333
    style F fill:#ccf,stroke:#333
    style G fill:#cfc,stroke:#333
```
</details>

Fig. 1. OA-SMPC operational framework with postprocessing.

$\mathbf { s } _ { 1 : q }$ is a hard constraint and does not vary with time, but as ${ \bf h } _ { 1 : r } ( t )$ is time-varying, we refer to these design limitations as time-varying, when considered together. After updating the states and control inputs, $h ( t + 1 )$ is updated by (18), and the optimization in (11) is repeated. The schematic of the complete OA-SMPC operational framework with postprocessing is shown in Fig. 1, with the algorithm presented in Algorithm 1. The results from Theorems 1–3 are independent of the postprocessing framework, and still hold with postprocessing. In addition, the restrictive one-step controllability in Assumption 3(b) can be relaxed for practical systems while still maintaining the structure of the input matrix in Remark 2 due to Assumption 6.

Note that the computational cost of the proposed OA-SMPC is the same as that of a nominal MPC, with the h update happening outside of the MPC framework which makes the present method extremely scalable for practical implementation. Also, similar to [3, Sec. VII], as the linear structure of the system is not leveraged for Theorems 1–3, the results hold for nonlinear systems too provided the relevant assumptions hold.

Example 1: To demonstrate postprocessing, consider a simple system with $\boldsymbol { x } ( t ) ~ = ~ \left[ x _ { 1 } ( t ) \right]$ as the state subjected to chance constraints, control inputs $u ( t ) ~ = ~ \left[ u _ { 1 } ( t ) u _ { 2 } ( t ) \right] ^ { 1 }$ , where $u _ { 1 }$ and $u _ { 2 }$ are the primary and secondary control sources, respectively, and the uncertainty is denoted by $w ( t )$ . The system matrices are A, $B = \left[ B _ { 1 1 } \begin{array} { l l } { 0 } \end{array} \right]$ and E. The structure of B follows from Assumption 6(b). $u _ { 1 } ( t )$ and $u _ { 2 } ( t )$ are coupled by (3), following Assumption 6(c). Let $\tilde { x } _ { 1 } ( t + 1 )$ and $\tilde { u } _ { 1 }$ be the state and primary control upper limits based on the time-varying design limitations following Assumption 6(a). The system described is similar to a MG with BESS, VRE, local load demand, and grid connectivity, where the BESS

## Algorithm 1 OA-SMPC

## Initialization

1. Choose $x ( 0 )$ .  
2. Choose $h ( 0 )$ from domain knowledge.

## Online solution

1. Solve (11) to get $u ^ { * } ( t | t ) .$ .

2a. If post-processing of nominal OA-SMPC computed optimal control inputs are not allowed: Set $u ( t ) \gets u ^ { * } ( t | t )$ and account for the uncertainties in x(t + 1) by (1). Calculate $V ( t + 1 )$ from (12a).

2b. If post-processing of nominal OA-SMPC computed optimal control inputs are allowed: Post-process by (24) and (3) to account for the uncertainties to calculate $x ( t + 1 )$ and $u ( t )$ . Calculate $V ( t + 1 )$ similar to (25).

3. Calculate $Y ( t + 1 )$ from (12b).

4. Calculate $h ( t + 1 )$ from (18).

5. Set $t ~ \gets ~ t + 1$ and repeat from Step 1 of Online solution.

SOC is the state, BESS charging/discharging power is the primary control source, and the grid import power is the secondary control source. Both the control sources are coupled by the grid power balance equation, while the uncertainty is the real-time VRES and load forecast error.

When correcting for the uncertainties in a closed-loop, first it is ensured that the primary control source handles only the part of the uncertainty that still keeps it within the feasible set. The altered primary control input can be formulated as follows:

$$
u _ {1} (t) := \min \left(u _ {1} ^ {*} (t | t) + D _ {1} w (t), \tilde {u} _ {1}\right) \tag {24a}
$$

where $\begin{array} { r } { D \ = \ B ^ { \dagger } E } \end{array}$ and $u _ { 1 } ^ { * } ( t | t )$ is the optimal MPC computed primary control input. The state update equation using Assumptions 6(a) and 6(b) is formulated as follows:

$$
x _ {1} (t + 1) = \min \Bigl (A x _ {1} (t) + B _ {1 1} u _ {1} (t), \tilde {x} _ {1} (t + 1) \Bigr). \tag {24b}
$$

If from $( 2 4 \mathrm { b } ) , \ x _ { 1 } ( t + 1 ) \ = \ \tilde { x } _ { 1 } ( t + 1 )$ , we re-compute, $u _ { 1 } ( t ) ~ = ~ ( B _ { 1 1 } ) ^ { - 1 } ( \tilde { x } _ { 1 } ( t + 1 ) - A x _ { 1 } ( t ) )$ . Finally, the altered secondary control input $u _ { 2 } ( t )$ is computed by satisfying (3). In the case of the time-varying design limitations giving the lower limits of the state and primary controls, $\tilde { x } _ { 1 } ( t + 1 )$ and $\tilde { u } _ { 1 } .$ , respectively, (24a) and (24b) are modified by replacing the min by the max function. Assumptions 6(a) and 6(b) ensure that the postprocessing steps give unique solutions, and Assumptions $6 ( \mathrm { c } )$ and (d) ensure that the correction for uncertainties affecting a chance constrained state and related control inputs does not unnecessarily affect other states and control inputs which may lead to inconsistency in the postprocessed solutions. After postprocessing, the state constraint violations in closed-loop are tracked as (25) with time-average of violations calculated similar to (12b)

$$
V _ {1} (t + 1) := \left\{ \begin{array}{l l} 1, & G _ {1} x (t + 1) > g _ {1} \\ 0, & G _ {1} x (t + 1) \leq g _ {1}. \end{array} \right. \tag {25}
$$

Remark 6 (Significance of the Post-Processing Framework): In related previous works with two mutually coupled sources of control to handle uncertainties on chance constrained states such as [17], the time-varying design limitations as that of Assumption 6(a) are not considered. Large primary control inputs to handle large uncertainties can, thus, potentially lead to damaging the primary controller in [17]. In other works like [3], MPC computed control inputs are not altered to account for uncertainties, making the state handle the entire uncertainty in a closed-loop. Large uncertainties can thus steer the system away from the OA-SMPC feasible region, which due to Assumption 3(a) can lead to the application of expensive control input at the next time-step to bring the system back to feasibility. Such an expensive control input can lead to high economic cost, a problem not considered in both [3] and [17].

Remark 7 (Nonconservative Chance Constraint Satisfaction in a “Practical Sense” in Closed-Loop Even Under Time-Varying Uncertainty Distribution, and Repeated Large Uncertainties): As the nominal OA-SMPC always has access to relaxed feasible states, it is possible for the predicted nominal solutions to always violate the (original) state constraints ∀k, t (as satisfaction of (9a) is not mandatory in our formulation). In closed-loop, thus, if solutions are continuously violated more than the maximum prescribed level, $h _ { i }$ increases until $h _ { i } \ \to \ 0 ^ { - }$ according to (18). Mathematical violations can still persist in $V _ { i } ( t + 1 )$ after $h _ { i } ~  ~ 0 ^ { - }$ which fails to give the benefit of adaptive relaxation. These violations can be ignored during practical implementation, by adding a small $\varepsilon ~ > ~ 0 ~ \mathrm { t o } ~ g _ { i }$ resulting in considering violations only if $G _ { i } x ( t + 1 ) > g _ { i } + \varepsilon$ . The consideration of ε in tracking violations is an operational step and can be removed by the user when $h _ { i }$ decreases sufficiently to relax the state constraint and reduce conservatism. This adaptive behavior of our system along with postprocessing ensures that the chance constraints in (4) are satisfied in a “practical sense” in closedloop, which previous works [3], [17], and [25] may not be able to satisfy under repeated large uncertainties when the mixed worst case disturbance sequence is not known a priori, or if the uncertainty distribution changes with time. Note that the mixed worst-case disturbance sequence computed via scenarios of uncertainty in [3] and [17] may in itself be overconservative [31].

## IV. CASE STUDY

## A. Overview

In this section, we implement our proposed method (OA-SMPC) for simulating the optimal BESS dispatch strategy for a practical MG with PV, load, and connection to the main grid in an EMPC framework. The MG setup is from the real-life MG at the Port of San Diego, described in [18]. The MG model incorporates electricity prices with demand and energy charges, realistic load and PV forecast, and a post-processing step for incorporating the forecast uncertainties in BESS dispatch. The yearly (2019) electricity costs were compared for the MG, for the traditional MPC, with hard constraints on the state (BESS SOC), and our OA-SMPC method with chance constraints on the state. The motivation for using chance constraints on BESS SOC in the OA-SMPC is to leverage some extra BESS capacity to reduce demand peaks and thus, demand charges, leading to significant electricity cost savings, while staying within a maximum violation probability bound to avoid adverse effects on BESS life. In addition, we also compare our proposed OA-SMPC to the traditional EMPC method without chance constraints, and a state-of-theart approach [3] from the literature with chance constraints and similar computational cost.

## B. PV and Load Forecast

The MG model uses day-ahead PV and gross load forecasts with 15 min time resolution as inputs. The k-nearest neighbor (kNN) algorithm is used for the gross load forecast. The training data comprises of 15-min resolution historical load observations from November 1, 2018 to November 20, 2019. For every MPC horizon, gross load observations for the previous 24 h (feature vector) are compared with the training sample and $k = 2 9$ nearest neighbors are identified by the kNN algorithm. Finally, the gross load forecast is calculated by averaging the gross load of the selected neighbors at every time step of the forecast horizon [32]. The root mean square error (RMSE), mean absolute error (MAE), and mean bias error (MBE) for the gross load forecast for the entire year are 22.4, 17.3, and −1.2 kW, respectively.

The PV generation forecast for the upcoming 24 h utilizes the kNN (with $k = 3 0 )$ algorithm as well. The feature vector is formed by three datasets: numerical weather prediction (NWP) model forecasts for the upcoming 24 h, the average of the preceding 1-, 2-, 3-, and 4-h PV power generation, and the current time of the day. The NWP forecast utilized was the high-resolution rapid refresh (HRRR) model developed by NOAA [33]. The PV power generation dataset was obtained by running simulations for the PV plant in the solar advisor model (SAM) using the irradiance observation data obtained from the NSRDB database as an input. The training sample for the PV power generation forecast includes NWP forecast and irradiance observations between January 1, 2019 and December 31, 2019. The RMSE, MAE, and MBE for the PV forecast for the entire year are 20.3, 9.1, and −0.8 kW, respectively.

## C. MG Model

The system state $x ( t ) = [ x _ { 1 } ( t ) ]$ is the BESS SOC. The control input is $u ( t ) \ = \ \big [ u _ { 1 } ( t ) u _ { 2 } ( t ) \big ] ^ { \ }$ , where $u _ { 1 } ( t )$ is the BESS dispatch power (primary control source for handling uncertainty), and $u _ { 2 } ( t )$ is the grid import power (secondary control source for handling uncertainty). $u _ { 1 } ( t ) > 0$ denotes charging, while $u _ { 2 } ( t ) > 0$ denotes power import from the main grid to the MG. The PV generation and gross load are denoted by $\mathrm { P V } ( t )$ and L(t), respectively, and are used as forecast inputs to the MPC. The uncertainty w $( t ) = [ w _ { 1 } ( t ) ]$ is the difference between the gross load and PV generation forecast uncertainties, i.e., $w _ { 1 } ( t ) = \left( L ^ { f } ( t ) - L ^ { r } ( t ) \right) - \left( \mathbf { P V } ^ { f } ( t ) - \mathbf { P V } ^ { r } ( t ) \right)$ , where the superscript f and r denote forecast and real values. The MPC prediction horizon is one-day ahead, subdivided into $N = 9 6$ equal time steps of $\Delta t = 0 . 2 5 \mathrm { ~ h ~ }$ (15 min) each.

The system matrices are $A = [ 1 ] , \ : B = \big \lceil ( \Delta t / \mathrm { B E S S } _ { \mathrm { e n } } ) \ : 0 \big \rceil ,$ , and $\begin{array} { r c l } { E } & { = } & { [ \Delta t / \mathrm { B E S S } _ { \mathrm { e n } } ] , } \end{array}$ , where $\mathrm { B E S S _ { \mathrm { e n } } }$ is the energy capacity of the BESS. The system matrices handle the SOC update of the battery due to charging/discharging. For the hard control input constraints, $S ~ = ~ \left[ \begin{array} { l l } { 1 } & { 0 } \\ { - 1 } & { 0 } \end{array} \right]$ and $\begin{array} { r } { s ~ { } = ~ \left[ \mathrm { B E S S } _ { \mathrm { m a x } } \quad \mathrm { B E S S } _ { \mathrm { m a x } } \right] ^ { \top } } \end{array}$ , which constrains the maximum charging/discharging power of the BESS. For the time-varying equality constraints coupling the control inputs, $M = \big [ 1 - 1 \big ] , \thinspace c ( t ) = [ \mathrm { P V } ^ { f } ( t ) - L ^ { f } ( t ) ]$ , and $F ~ = ~ [ 1 ]$ , which ensures power balance of the MG with the main grid. The MPC also has a terminal state constraint defined as $x _ { 1 } ( t + N | t ) \geq \hat { x } _ { 1 }$ .

For this case study, we consider JCC on the BESS SOC which considers a violation if the BESS SOC goes above or below predefined upper $\mathrm { ( S O C _ { m a x } ) }$ and lower bounds $( \mathrm { S O C } _ { \mathrm { m i n } } )$ in closed-loop. The goal is to reduce electricity import costs from the grid by having controlled violations beyond the predefined upper and lower bounds by making a larger BESS capacity available for dispatch. Unrestricted violations are avoided as they can adversely affect the BESS lifetime. The chance constraints are defined by $G ~ = ~ { \left[ 1 \mathrm { ~  ~ \Gamma ~ } - 1 \right] } ^ { \top }$ , $\begin{array} { r l } { g \ = \ \left[ \mathrm { S O C } _ { \operatorname* { m a x } } \right. } & { { } \left. - \mathrm { S O C } _ { \operatorname* { m i n } } \right] ^ { \intercal } } \end{array}$ and $h ( t ) \ = \ \left[ h _ { 1 } ( t ) \quad h _ { 2 } ( t ) \right] ^ { ! }$ . We choose $h _ { 1 } ( t ) = h _ { 2 } ( t )$ for adapting both the state constraints simultaneously by the same parameter as they are setup in JCC form. The maximum probability of the JCC violation is predefined by $\alpha .$ For the traditional EMPC (without chance constraints), violations are avoided and the state constraints are formulated as (26), while for the OA-SMPC the state constraints are formulated as (10)

$$
G x (t + k | t) \leq g \quad \forall k \in \mathbb {N} _ {1} ^ {N} \forall t. \tag {26}
$$

In the JCC formulation, when updating h(t) by (18), it is ensured that $h _ { 1 } ( t ) = \operatorname* { m a x } \bigl ( \mathrm { S O C } _ { \mathrm { m a x } } - 1 , h _ { 1 } ( t ) \bigr )$ , and $h _ { 2 } ( t ) =$ max $\left( - \mathrm { S O C } _ { \operatorname* { m i n } } , h _ { 2 } ( t ) \right)$ to ensure the state constraints do not violate physical limits of SOC above 1 or below 0. However, in our case study, $h _ { i } ( t )$ given by (18) never violate physical limits, obviating the above correction. We have practically ensured this by setting a high value of $\gamma _ { i }$ in (18), which is a design choice, at the cost of slower system adaptation $( \mathrm { i . e . } ,$ , rate of change of $h _ { i } ( t ) )$ , and setting the initial constraint relaxation parameter $h _ { i } ( 0 )$ such that $g _ { i } - h _ { i } ( 0 )$ is sufficiently far away from physical limits for $i \in \{ 1 , 2 \}$ .

The objective function is formulated as in [18] and [34], and is given by

$$
\begin{array}{l} J (t) = R _ {\mathrm{NC}} \max \left\{u _ {2} (t + k | t) \right\} _ {k = 0} ^ {N - 1} + R _ {\mathrm{OP}} \max \left\{u _ {2} (t + l | t) \right\} _ {l \in \mathbb {I} (t)} \\ + R _ {\mathrm{EC}} \Delta t \left[ \sum_ {k = 0} ^ {N - 1} u _ {2} (t + k | t) + \frac {1 - \eta}{2} \sum_ {k = 0} ^ {N - 1} \left| u _ {1} (t + k | t) \right| \right] \tag {27} \\ \end{array}
$$

where $R _ { \mathrm { N C } }$ is the NCDC rate charged on the maximum grid import during the prediction horizon. Similarly, $R _ { \mathrm { O P } }$ is the OPDC rate, charged on the maximum grid import between 16:00 and 21:00 h, called OP hours of the prediction horizon, and I(t) represents indices of prediction horizon time steps coinciding with the OP hours. Naturally, the indices of OP hours in the prediction horizon are a function of the starting time step t of the MPC prediction horizon. $R _ { \mathrm { E C } }$ is the energy charge rate and $\eta$ is the round-trip efficiency of the BESS accounting for BESS losses. After the net load for the entire month is realized, the monthly NCDC is computed based on maximum load demand from the grid during the month, while the monthly OPDC is computed based on maximum load demand between 16:00 and 21:00 h of all days of the month. The predefined parameters of the MG for the OA-SMPC operation are shown in Table I, and a block diagram of the MG operational framework is shown in Fig. 1. Note that the one step controllability in Assumption 3(b), and the ideal control policy in Assumption 5 is relaxed for the case study for realistic simulations. The satisfaction of Assumption 3(a) is ensured by choosing a sufficiently large $\gamma _ { i }$ which constrains the rate of $h _ { i } ( t )$ increase depending on the available control input power to satisfy $( \mathsf { A } . 5 )$ . Note that due to Assumption 6(a), in our case study, the risk of violation of Assumption 3(a) can only arise when $h _ { i } ( t )$ increases.

TABLE I DESIGN PARAMETERS OF OA-SMPC FOR MG

<table><tr><td>Parameter</td><td>Symbol</td><td>Value</td></tr><tr><td>NCDC rate</td><td> $R_{\text{NC}}$ </td><td>$24.48/kW</td></tr><tr><td>OPDC rate</td><td> $R_{\text{OP}}$ </td><td>$19.19/kW</td></tr><tr><td>Energy rate</td><td> $R_{\text{EC}}$ </td><td>$0.1/kWh</td></tr><tr><td>BESS round-trip efficiency</td><td> $\eta$ </td><td>0.8</td></tr><tr><td>BESS energy capacity</td><td> $BESS_{\text{en}}$ </td><td>2,500 kWh</td></tr><tr><td>BESS power capacity</td><td> $BESS_{\text{max}}$ </td><td>700 kW</td></tr><tr><td>Upper bound of SOC for traditional EMPC 1</td><td> $SOC_{\text{max}}$ </td><td>0.8</td></tr><tr><td>Lower bound of SOC for traditional EMPC 1</td><td> $SOC_{\text{min}}$ </td><td>0.2</td></tr><tr><td>Maximum violation probability</td><td> $\alpha$ </td><td>0.1</td></tr><tr><td>Terminal state constraint</td><td> $\hat{x}_{1}$ </td><td>0.5</td></tr><tr><td>Initial state</td><td> $x_{1}(0)$ </td><td>0.5</td></tr><tr><td>Initial constraint relaxing parameter</td><td> $h(0)$ </td><td> $\left[-0.1 -0.1\right]^{\top}$ </td></tr><tr><td>Proportionality constant</td><td> $\gamma_{1}$  and  $\gamma_{2}$ </td><td>15</td></tr></table>

## D. Operation Strategy

This section presents the real-time operation strategy of the OA-SMPC for the economic MG dispatch problem under consideration. Although (18) updates $h _ { i }$ at every time step, given the emphasis on additional OPDC penalties in our cost function, whenever the starting time step of the MPC coincides with daily OP hours, we restrict $h _ { i }$ increase between two time steps, overriding (18) when required. Decreasing $h _ { i }$ during the daily OP hours is still allowed, and if $h _ { i }$ increases it is reset manually to the previous value. The reasoning behind restricting the increase of $h _ { i }$ during OP times is to avoid the additional OPDC costs (on top of NCDC) by incurring preferential violations during $\mathrm { O P }$ hours (by deeper BESS discharge due to relaxed state constraints). The preferential $\mathrm { O P }$ hour violations may lead to state violations beyond the maximum allowable limit temporarily, but the adaptive rule

7The maximum absolute forecast uncertainty throughout the year for the case study is 261 kW, which still is lesser than half of the BESS power capacity of 700 kW. Thus, even in the case where Assumption 6(b) is relaxed, and the BESS is set up to handle all the uncertainty until reaching the physical limits, the practical viability of Assumption 3(a) is reinforced.

TABLE II RESULTS FOR THE FOUR TEST CASES FOR THE YEAR 2019. MONTHLY COSTS ARE ADDED TO COMPUTE THE YEARLY COST

<table><tr><td>Costs</td><td>Traditional 1</td><td>OA-SMPC</td><td>SMPC Lit [3]</td><td>Traditional 2</td></tr><tr><td>NCDC</td><td>$66,586</td><td>$64,579</td><td>$66,960</td><td>$57,690</td></tr><tr><td>OPDC</td><td>$1,959</td><td>$1,274</td><td>$2,087</td><td>$1,148</td></tr><tr><td>Energy Cost</td><td>$14,382</td><td>$14,385</td><td>$14,382</td><td>$14,399</td></tr><tr><td>BESS loss</td><td>$8,290</td><td>$9,071</td><td>$8,309</td><td>$10,447</td></tr><tr><td>Total Cost</td><td>$91,217</td><td>$89,309</td><td>$91,738</td><td>$83,683</td></tr><tr><td>Total BESS cycles</td><td>165.8</td><td>181.4</td><td>166.2</td><td>208.9</td></tr><tr><td>Y at year end</td><td>0%</td><td>10.1%</td><td>2.2%</td><td>20.4%</td></tr></table>

(by virtue of $Y \ > \ \alpha )$ pushes the BESS to compensate by lowering violations due to rapid increase of $h _ { i }$ during other times (when the risk of penalty on peaks is lower). The simulations are carried out in CVX, a package for solving convex programs in the MATLAB environment [35], [36].

## V. RESULTS AND DISCUSSION

We compare results of yearly simulations from four test cases.

1) Traditional EMPC 1 described in Section IV-C to demonstrate the case without chance constraints.  
2) OA-SMPC which is our proposed method described in Section IV-C to demonstrate the case with chance constraints on state.  
3) SMPC Lit from [3] which is similar in computational cost to our proposed method, with the chance constraints being represented by (9b), a corresponding adaptive constraint tightening rule given by $\tilde { h } _ { i } ( t ) = \tilde { h } _ { i } ( t - 1 )$  
4) Traditional EMPC 2 which modifies Case (i) with $g =$ $\left[ \mathrm { S O C } _ { \mathrm { m a x } } - h _ { 1 } ( 0 ) \right. \left. - \mathrm { S O C } _ { \mathrm { m i n } } - h _ { 2 } ( 0 ) \right] ^ { \mathrm { l } }$ to demonstrate the MG performance with the state constraints always relaxed by the same initial relaxing parameter in Case (ii) to violate $\mathrm { S O C _ { \mathrm { m a x } } / S O C _ { \mathrm { m i n } } }$ in closed-loop but without adaptation.

$$
\begin{array}{l} \left[ 1 - \frac {\alpha_ {i} - Y _ {i} (t) + \frac {2 \alpha_ {i} - 1}{2 t}}{\gamma_ {i}} \right], \text { with } \tilde {h} _ {i} (t) > 0, \forall t, i \in \{1, 2 \}, \text { and } \\ \tilde {h} _ {1} (t) = \tilde {h} _ {2} (t). \text { As   [3]   only   tightens   the   state   constraints, } \\ \text { violation   is   allowed   in   closed - loop   by   changing   the } \\ \text { feasible   state   set   in   the   postprocessing   step   in   Assumption   6(a)   to   the   physical   limits   of   the   system   (i.e.,   SOC } \\ \text { limits   of   0   and   1). Similar   to   the   OA - SMPC, } \tilde {h} _ {i} \text { increase } \\ \text { during   OP   hours   is   avoided   by   resetting   it   manually   to } \\ \text { its   previous   value. The   design   parameters   for   SMPC   Lit } \\ \text { are   the   same   as   that   of   OA - SMPC(see   TableI),with} \\ \tilde {h} (0) = - h (0). \end{array}
$$

From the PV and gross load forecasting errors, we find that the mean and standard deviation (SD) of the uncertainty are −0.5 and 30.3 kW, respectively, for the entire year of 2019. A greater (lesser) value of mean uncertainty would push the states higher (lower) in general causing more constraint violations due to the BESS exceeding (falling below) the $\mathrm { S O C } _ { \mathrm { m a x } } \ : ( \mathrm { S O C } _ { \mathrm { m i n } } )$ . A higher SD of the uncertainty would cause the closed-loop behavior of the system to differ significantly from the open-loop solutions given by (11) which in addition to increasing the likelihood of violations may also compel expensive BESS dispatch to handle the uncertainty and satisfy Assumption 3(a).

![](images/fd3a9b5e7f9341677dcdca17ccda435adf85c95b9ffc0d52969d35345749d3db.jpg)

<details>
<summary>line chart</summary>

| Time of the year | Violation level (α) | Violation level (SMPC Lit [3]) | Violation level (Trad. EMPC 2) | h₁ or h₁ (Power u₁) | h₁ or h₁ (u₂) | Power (kW) | SOC (x₁) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Jan | ~0.1 | ~0.05 | ~0.2 | ~-0.1 | ~-0.1 | ~0 | ~0 |
| Feb | ~0.05 | ~0.05 | ~0.2 | ~-0.1 | ~-0.1 | ~0 | ~0 |
| Mar | ~0.05 | ~0.05 | ~0.2 | ~-0.1 | ~-0.1 | ~0 | ~0 |
| Apr | ~0.05 | ~0.05 | ~0.2 | ~-0.1 | ~-0.1 | ~0 | ~0 |
| May | ~0.05 | ~0.05 | ~0.2 | ~-0.1 | ~-0.1 | ~0 | ~0 |
| Jun | ~0.05 | ~0.05 | ~0.2 | ~-0.1 | ~-0.1 | ~0 | ~0 |
| Jul | ~0.05 | ~0.05 | ~0.2 | ~-0.1 | ~-0.1 | ~0 | ~0 |
| Aug | ~0.05 | ~0.05 | ~0.2 | ~-0.1 | ~-0.1 | ~0 | ~0 |
| Sep | ~0.05 | ~0.05 | ~0.2 | ~-0.1 | ~-0.1 | ~0 | ~0 |
| Oct | ~0.05 | ~0.05 | ~0.2 | ~-0.1 | ~-0.1 | ~0 | ~0 |
| Nov | ~0.05 | ~0.05 | ~0.2 | ~-0.1 | ~-0.1 | ~0 | ~0 |
| Dec | ~0.05 | ~0.05 | ~0.2 | ~-0.1 | ~-0.1 | ~0 | ~0 |
| Jan | ~0.05 | ~0.05 | ~0.2 | ~-0.1 | ~-0.1 | ~0 | ~0 |
| Feb | ~0.1 | ~0.1 | ~0.2 | - | - | ~1 | ~1 |
| Mar | ~0.1 | ~0.1 | ~0.2 | - | - | ~1 | ~1 |
| Apr | ~0.1 | ~0.1 | ~0.2 | - | - | ~1 | ~1 |
| May | ~0.1 | ~0.1 | ~0.2 | - | - | ~1 | ~1 |
| Jun | ~0.1 | ~0.1 | ~0.2 | - | - | ~1 | ~1 |
| Jul | ~0.1 | ~0.1 | ~0.2 | - | - | ~1 | ~1 |
| Aug | ~0.1 | ~0.1 | ~0.2 | - | - | ~1 | ~1 |
| Sep | ~0.1 | ~0.1 | ~0.2 | - | - | ~1 | ~1 |
| Oct | ~0.1 | ~0.1 | ~0.2 | - | - | ~1 | ~1 |
| Nov | ~0.1 | ~0.1 | ~0.2 | - | - | ~1 | ~1 |
| Dec | ~0.1 | ~0.1 | ~0.2 | - | - | ~1 | ~1 |
| Jan | 0 | 0 | 0 | 1 | 1 | 1 | 1 |
| Feb | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| Mar | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| Apr | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| May | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| Jun | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| Jul | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| Aug | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| Sep | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| Oct | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| Nov | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| Dec | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
</details>

Fig. 2. Yearly time series for (a) time average of state constraint violations (Y ) for the OA-SMPC, SMPC Lit [3], and Traditional EMPC 2 case studies, and the maximum allowable state constraint violation probability $( \alpha = 0 . 1 ) , ( \mathbf { b } )$ adaptive state constraint relaxing parameters $( h _ { 1 }$ and ${ \tilde { h } } _ { 1 } ,$ , with $h _ { 1 } = h _ { 2 }$ and $\tilde { h } _ { 1 } \stackrel { = } { = } \tilde { h } _ { 2 }$ in these case studies) for the OA-SMPC and SMPC Lit, (c) closed-loop behavior of the control inputs for the OA-SMPC, and (d) closed-loop behavior of the state for the OA-SMPC.

Fig. 2 and Table II summarize the simulation results of the four test cases for the year 2019. Note that the energy costs are similar across all cases as there is no arbitrage in the MG model. The minute differences in energy costs are due to the different ending BESS SOC at the end of the year for different test cases.

The comparison between Traditional EMPC 1 and OA-SMPC demonstrates the superior economic performance of our proposed algorithm while still staying within the maximum allowable violation probability bound. NCDC and OPDC decrease by 3% and 35% between Traditional EMPC 1 and OA-SMPC. The OPDC savings (as a percentage) are significantly more than NCDC because of the operation strategy (see Section IV-D) allowing preferential violations during OP hours. As extra BESS capacity is available for the OA-SMPC as compared to the Traditional EMPC 1 due to the adaptive relaxation of BESS SOC constraints beyond the 0.2–0.8 SOC range, the BESS is used more aggressively in OA-SMPC resulting in 9.4% more BESS losses amounting to 15.6 extra BESS yearly cycles. Overall, OA-SMPC leads to 2.1% total yearly cost savings as compared to the Traditional EMPC 1. SMPC Lit performs the worst as it tightens the feasible SOC range and leads to highest yearly cost. Some violations are caused due to the effect of uncertainty in the closed-loop but it is unable to reduce costs as the timing of the uncertainty is critical in reducing demand charges. NCDC and OPDC are greater in SMPC Lit as compared with both Traditional EMPC 1 and OA-SMPC. SMPC Lit has similar BESS cycles as the Traditional EMPC 1 with 0.6% extra yearly costs. This serves as a proof of the concept alluded to in Remark 1 that adaptive constraint tightening methods (like SMPC Lit) can be over-conservative being unable to exploit the allowable violation limit which can be particularly disadvantageous in EMPC frameworks.

Fig. 2(a) shows the variation of Y for the OA-SMPC (green line), and SMPC Lit (black dotted line) for the entire year. For the OA-SMPC, initially, Y is 0 until the first constraint violation occurs, after which it overshoots $\alpha ,$ oscillating with a high frequency and magnitude until the first week of January. Then, consistent with the goal of Y converging to a value less than or equal to $\alpha ,$ Y decreases and oscillates about α with lower frequency and magnitude, as the system evolves, and finally reaching a value of 10.1% at the end of the year (which is within a small margin of α). It is expected for Y to decrease below α if the OA-SMPC is run over a longer time period, resulting in satisfying the chance constraints, albeit nonconservatively, in a closed-loop. Fig. 2(b) shows that for the OA-SMPC, $h _ { 1 }$ and $h _ { 2 }$ increase and decrease depending on the overshoot and undershoot of Y with respect to α, respectively, being able to expand and contract the feasible state set accordingly to keep Y near α. Fig. 2(c) and (d) demonstrates the yearly closed-loop behavior of the BESS dispatch and grid import, and SOC, respectively, for the OA-SMPC. For a significant portion of the year, the grid import is negative because the PV system at the location is oversized generating excess power which is fed back to the grid. Significant violation of constraints can be seen in Fig. 2(d) in the months of $\mathbf { M a y }$ and October, which are the two months that aid most in cost savings.

Fig. 2(b) also shows that for the SMPC Lit, the $\tilde { h } _ { 1 }$ and $\tilde { h } _ { 2 }$ quickly drop down to 0 in the first week of January trying to encourage violations initially as the initial constraint tightening was too conservative. However, the system’s Y still stays far below α, with the result that $\tilde { h } _ { 1 }$ and $\tilde { h } _ { 2 }$ stay close to $0 ^ { + }$ throughout the year behaving essentially like Traditional EMPC 1 in the nominal MPC with allowance for SOC limits to range from 0 to 1 in closed-loop. In the months of February and March (not shown in figures), SMPC Lit creates more NCDC than Traditional EMPC 1, because just before the NCDP time, a high uncertainty forces the SOC to go below the lower limit of 0.2, which causes a large BESS charging action at the next time step to climb back to the feasible SOC range of above 0.2 causing demand peaks, a problem alluded to in Remark 6.

Traditional EMPC 2 demonstrates that relaxing the state (BESS SOC) constraints by the same initial relaxation parameter as in OA-SMPC without any adaptation causes the violations to exceed the maximum allowable violation probability bound in closed-loop [blue line in Fig. 2(a)]. Traditional EMPC 2 has lower total yearly electricity costs (due to lower NCDC and OPDC), than the OA-SMPC because the objective function in (27) penalizes peaks in grid import power $\left( u _ { 2 } \right)$ more than BESS dispatch power (u ), resulting in prolonged aggressive dispatch of the BESS to serve the net load $( L -$ PV). The aggressive BESS dispatch is more prolonged in Traditional EMPC 2 than OA-SMPC because the feasible BESS SOC range in Traditional EMPC 2 is not adapted due to past violations, and the feasible state set continues being larger than OA-SMPC throughout. Traditional EMPC 2 shows a higher frequency of oscillation and magnitude of overshoot of Y above α as compared to OA-SMPC, and additionally shows that Y consistently remains above 2α for the majority of the year. Thus, Traditional EMPC 2 is unable to fulfill the chance constraints and may significantly harm BESS life, resulting in 27.5 more yearly BESS cycles as compared with OA-SMPC. The analysis serves as a proof of concept that it is the adaptive relaxation rule in OA-SMPC rather than the nature of uncertainties that ensure nonconservative chance constraint satisfaction in closed-loop in OA-SMPC.

TABLE III RESULTS FOR THE OA-SMPC WITH DIFFERENT VALUES OF MAXIMUM PROBABILITY OF VIOLATION OF STATE CONSTRAINTS (α) FOR THE YEAR 2019

<table><tr><td>Costs</td><td> $\alpha = 0.05$ </td><td> $\alpha = 0.15$ </td><td> $\alpha = 0.2$ </td></tr><tr><td>NCDC</td><td>$66,294</td><td>$59,564</td><td>$57,323</td></tr><tr><td>OPDC</td><td>$1,865</td><td>$1,036</td><td>$540</td></tr><tr><td>Energy Cost</td><td>$14,383</td><td>$14,395</td><td>$14,395</td></tr><tr><td>BESS loss</td><td>$8,473</td><td>$9,747</td><td>$10,336</td></tr><tr><td>Total Cost</td><td>$91,014</td><td>$84,742</td><td>$82,593</td></tr><tr><td>Total BESS cycles</td><td>169.5</td><td>194.9</td><td>206.7</td></tr><tr><td>Y at year end</td><td>4.5%</td><td>14.3%</td><td>19.1%</td></tr></table>

![](images/87743542ca86f2e92070f65abcc9c82696bc2f3a272ae57f46fb87b4f3a20898.jpg)

<details>
<summary>line chart</summary>

| Time of the year | Violation level (α) | Violation level (Y₀.₀₅) | Violation level (Y₀.₁₅) | Violation level (Y₀.₂) |
| ---------------- | ------------------- | ------------------------ | ------------------------ | ----------------------- |
| Jan              | 0.2                 | 0.1                      | 0.1                      | 0.2                     |
| Feb              | 0.15                | 0.05                     | 0.1                      | 0.2                     |
| Mar              | 0.1                 | 0.05                     | 0.1                      | 0.2                     |
| Apr              | 0.1                 | 0.05                     | 0.1                      | 0.2                     |
| May              | 0.1                 | 0.05                     | 0.1                      | 0.2                     |
| Jun              | 0.1                 | 0.05                     | 0.1                      | 0.2                     |
| Jul              | 0.1                 | 0.05                     | 0.1                      | 0.2                     |
| Aug              | 0.1                 | 0.05                     | 0.1                      | 0.2                     |
| Sep              | 0.1                 | 0.05                     | 0.1                      | 0.2                     |
| Oct              | 0.1                 | 0.05                     | 0.1                      | 0.2                     |
| Nov              | 0.1                 | 0.05                     | 0.1                      | 0.2                     |
| Dec              | 0.1                 | 0.05                     | 0.1                      | 0.2                     |
| Jan              | 0.1                 | 0.05                     | 0.1                      | 0.2                     |
</details>

Fig. 3. Yearly time series for the time average of state constraint violations $( \bar { Y _ { \alpha } } )$ for the OA-SMPC for the maximum allowable state constraint violation probability α ∈ {0.05, 0.15, 0.2}.

Fig. 3 and Table III present the results of the OA-SMPC analysis when it is repeated with different values of α ∈ {0.05, 0.15, 0.2}, with the same other design parameters as in Table I. Fig. 3 shows that in all the cases, similar behavior is observed as from Fig. 2(a) with Y oscillating about α with lower frequency and magnitude as the system evolves with time along the year while ultimately settling at a value below α at year-end, thereby tightly satisfying the chance constraints. Table III demonstrates that superior cost savings are attained when α increases at the cost of more BESS cycles. Comparing the case with $\alpha = 0 . 2$ in Table III with Traditional EMPC 2 in Table II shows that the OA-SMPC is more effective at reducing costs with less violations and BESS cycles than Traditional EMPC 2 due to the OA-SMPC’s online adaptivity.

## VI. CONCLUSION AND FUTURE WORK

This work presents a novel online adaptive state constraint relaxation-based SMPC (OA-SMPC) framework for nonconservative chance constraint satisfaction in closed-loop. An adaptive state constraint relaxation rule is developed for a generic discrete LTI system based on the time average of past constraint violations without any a priori assumptions about the probability distribution of the uncertainty set or its statistics, or sample uncertainties from historical data. The time average of the state constraint violations, under the assumption of ideal control inputs that can cause/prevent constraint violations almost surely, is proven to asymptotically converge to the maximum allowable violation probability. The time average of the state constraint violations is also proven to exhibit martingale-like behavior asymptotically, even without the ideal control input assumption.

The proposed method (OA-SMPC) is applied to minimize monthly electricity costs by optimal BESS dispatch for a grid-connected MG in an EMPC framework. We perform simulations for the Port of San Diego MG using realistic PV and load forecast data for the year 2019. Chance constraints are applied on the BESS SOC to make use of excess BESS capacity in our proposed OA-SMPC as compared with the traditional EMPC without chance constraints (which uses hard constraints on BESS SOC). The OA-SMPC outperforms the traditional EMPC and a state-of-the-art chance constrained approach from the literature, striking an effective tradeoff between high BESS utilization (i.e., higher cost savings) and full SOC constraint satisfaction (i.e., longer BESS lifetime). The OA-SMPC lowers MG electricity costs by nonconservative chance constraint satisfaction in closed-loop, thereby having minimal adverse effect on BESS lifetime. Future work will incorporate the BESS degradation cost and a life-cycle analysis of the MG.

## APPENDIX

A test for Assumption 3(a) entails solving the following optimization problem [37, Sec. 5.8.1] at each time t, given x(t), c(t|t), and h(t)

$$
f ^ {*} = \min 0 \tag {A.1}
$$

$$
\text { s.t. } x (t + 1 | t) = A x (t) + B u (t | t) \tag {A.2}
$$

$$
S u (t | t) \leq s \tag {A.3}
$$

$$
M u (t | t) = c (t | t) \tag {A.4}
$$

$$
G (x (t + 1 | t)) \leq g - h (t). \tag {A.5}
$$

If $f ^ { * } = 0 .$ , then the optimization problem defined above is feasible and we can guarantee Assumption 3(a) holds. If $f ^ { * } =$ ∞, then the optimization problem above is infeasible and Assumption 3(a) fails to hold. Note that if (3) and (11d) are not considered in the problem formulation, we can drop (A.4).

## ACKNOWLEDGMENT

The authors would like to thank Dr. Sonia Martínez Professor of Mechanical and Aerospace Engineering, University of California at San Diego (UCSD), San Diego, CA, USA, for the fruitful discussions during writing the article. They would also like to extend their deepest gratitude to the anonymous reviewers whose comments greatly improved the content of this article.

## REFERENCES

[1] P. Kou, F. Gao, and X. Guan, “Stochastic predictive control of battery energy storage for wind farm dispatching: Using probabilistic wind power forecasts,” Renew. Energy, vol. 80, pp. 286–300, Aug. 2015.  
[2] S. Singh, M. Pavone, and J.-J. E. Slotine, “Tube-based MPC: A contraction theory approach,” Tech. Rep., 2016. [Online]. Available: https://web.stanford.edu/\~pavone/papers/Singh.Pavone.Slotine. CDC16EV.pdf  
[3] F. Oldewurtel, D. Sturzenegger, P. M. Esfahani, G. Andersson, M. Morari, and J. Lygeros, “Adaptively constrained stochastic model predictive control for closed-loop constraint satisfaction,” in Proc. Amer. Control Conf., Jun. 2013, pp. 4674–4681.  
[4] D. Muñoz-Carpintero, G. Hu, and C. J. Spanos, “Stochastic model predictive control with adaptive constraint tightening for non-conservative chance constraints satisfaction,” Automatica, vol. 96, pp. 32–39, Oct. 2018.  
[5] Y. Long and L. Xie, “Iterative learning stochastic MPC with adaptive constraint tightening for building HVAC systems,” IFAC-PapersOnLine, vol. 53, no. 2, pp. 11577–11582, 2020.  
[6] M. Korda, R. Gondhalekar, F. Oldewurtel, and C. N. Jones, “Stochastic MPC framework for controlling the average constraint violation,” IEEE Trans. Autom. Control, vol. 59, no. 7, pp. 1706–1721, Jul. 2014.  
[7] F. Oldewurtel, L. Roald, G. Andersson, and C. Tomlin, “Adaptively constrained stochastic model predictive control applied to security constrained optimal power flow,” in Proc. Amer. Control Conf. (ACC), Jul. 2015, pp. 931–936.  
[8] X. Guo, Z. Bao, H. Lai, and W. Yan, “Model predictive control considering scenario optimisation for microgrid dispatching with wind power and electric vehicle,” J. Eng., vol. 2017, no. 13, pp. 2539–2543, Jan. 2017.  
[9] G. Liu, M. Starke, B. Xiao, X. Zhang, and K. Tomsovic, “Microgrid optimal scheduling with chance-constrained islanding capability,” Electr. Power Syst. Res., vol. 145, pp. 197–206, Apr. 2017.  
[10] Z.-P. Yuan, J. Xia, and P. Li, “Two-time-scale energy management for microgrids with data-based day-ahead distributionally robust chanceconstrained scheduling,” IEEE Trans. Smart Grid, vol. 12, no. 6, pp. 4778–4787, Nov. 2021.  
[11] K. Garifi, K. Baker, B. Touri, and D. Christensen, “Stochastic model predictive control for demand response in a home energy management system; stochastic model predictive control for demand response in a home energy management system,” in Proc. IEEE Power Energy Soc. Gen. Meeting (PESGM), Portland, OR, USA, Aug. 2018, pp. 1–5. [Online]. Available: https://ieeexplore.ieee.org/document/8586485  
[12] M. Gulin, J. Matuško, and M. Vašak, “Stochastic model predictive control for optimal economic operation of a residential DC microgrid,” in Proc. IEEE Int. Conf. Ind. Technol. (ICIT), Mar. 2015, pp. 505–510.  
[13] Y. Ding, T. Morstyn, and M. D. McCulloch, “Distributionally robust joint chance-constrained optimization for networked microgrids considering contingencies and renewable uncertainty,” IEEE Trans. Smart Grid, vol. 13, no. 3, pp. 2467–2478, May 2022.  
[14] F. H. Aghdam, N. T. Kalantari, and B. Mohammadi-Ivatloo, “A stochastic optimal scheduling of multi-microgrid systems considering emissions: A chance constrained model,” J. Cleaner Prod., vol. 275, Dec. 2020, Art. no. 122965.  
[15] H. Wang, H. Xing, Y. Luo, and W. Zhang, “Optimal scheduling of micro-energy grid with integrated demand response based on chanceconstrained programming,” Int. J. Electr. Power Energy Syst., vol. 144, Jan. 2023, Art. no. 108602.  
[16] O. Ciftci, M. Mehrtash, and A. Kargarian, “Data-driven nonparametric chance-constrained optimization for microgrid energy management,” IEEE Trans. Ind. Informat., vol. 16, no. 4, pp. 2447–2457, Apr. 2020.  
[17] X. Guo, Z. Bao, Z. Li, and W. Yan, “Adaptively constrained stochastic model predictive control for the optimal dispatch of microgrid,” Energies, vol. 11, no. 1, p. 243, Jan. 2018.  
[18] A. Ghosh, C. Cortes-Aguirre, Y.-A. Chen, A. Khurram, and J. Kleissl, “Adaptive chance constrained MPC under load and PV forecast uncertainties,” in Proc. IEEE PES Grid Edge Technol. Conf. Exposit. (Grid Edge), Apr. 2023, pp. 1–5.  
[19] B. Kouvaritakis, M. Cannon, and D. Muñoz-Carpintero, “Efficient prediction strategies for disturbance compensation in stochastic MPC,” Int. J. Syst. Sci., vol. 44, no. 7, pp. 1344–1353, Jul. 2013.  
[20] B. Kouvaritakis, M. Cannon, S. V. Rakovic, and Q. Cheng, “Explicit use´ of probabilistic distributions in linear predictive control,” Automatica, vol. 46, no. 10, pp. 1719–1724, Oct. 2010.  
[21] M. Farina, L. Giulioni, L. Magni, and R. Scattolini, “An approach to output-feedback MPC of stochastic linear discrete-time systems,” Automatica, vol. 55, pp. 140–149, May 2015.  
[22] G. C. Calafiore and L. Fagiano, “Stochastic model predictive control of LPV systems via scenario optimization,” Automatica, vol. 49, no. 6, pp. 1861–1866, 2013.  
[23] D. Bernardini and A. Bemporad, “Scenario-based model predictive control of stochastic constrained linear systems,” in Proc. 48th IEEE Conf. Decis. Control (CDC), Shanghai, China, Dec. 2009, pp. 6333–6338.  
[24] J. Fleming and M. Cannon, “Time-average constraints in stochastic model predictive control,” in Proc. Amer. Control Conf. (ACC), May 2017, pp. 5648–5653.  
[25] A. Capone, T. Brüdigam, and S. Hirche, “Online constraint tightening in stochastic model predictive control: A regression approach,” IEEE Trans. Autom. Control, vol. 70, no. 2, pp. 736–750, Feb. 2025.  
[26] J. B. Rawlings, D. Angeli, and C. N. Bates, “Fundamentals of economic model predictive control,” in Proc. IEEE 51st IEEE Conf. Decis. Control (CDC), Oct. 2012, pp. 3851–3861.  
[27] S. Mullendore, “An introduction to demand charges,” Clean Energy Group, Nat. Renew. Energy Lab. (NREL), Denver, CO, USA, Tech. Rep., 2017. [Online]. Available: https://www.cleanegroup.org/wpcontent/uploads/Demand-Charge-Fact-Sheet.pdf  
[28] D. Williams, Probability With Martingales. Cambridge, U.K.: Cambridge Univ. Press, 1991.  
[29] R. G. Bartle and R. G. Bartle, The Elements of Real Analysis, vol. 2. New York, NY, USA: Wiley, 1964.  
[30] G. Schildbach, G. C. Calafiore, L. Fagiano, and M. Morari, “Randomized model predictive control for stochastic linear systems,” in Proc. Amer. Control Conf. (ACC), Jun. 2012, pp. 417–422.  
[31] M. Lorenzen, F. Dabbene, R. Tempo, and F. Allgöwer, “Constrainttightening and stability in stochastic model predictive control,” IEEE Trans. Autom. Control, vol. 62, no. 7, pp. 3165–3177, Jul. 2017.  
[32] R. Zhang, Y. Xu, Z. Y. Dong, W. Kong, and K. P. Wong, “A composite k-nearest neighbor model for day-ahead load forecasting with limited temperature forecasts,” in Proc. IEEE Power Energy Soc. Gen. Meeting (PESGM), Jul. 2016, pp. 1–5.  
[33] S. G. Benjamin et al., “A North American hourly assimilation and model forecast cycle: The rapid refresh,” Monthly Weather Rev., vol. 144, no. 4, pp. 1669–1694, Apr. 2016.  
[34] A. Ghosh, M. Z. Zapata, S. Silwal, A. Khurram, and J. Kleissl, “Effects of number of electric vehicles charging/discharging on total electricity costs in commercial buildings with time-of-use energy and demand charges,” J. Renew. Sustain. Energy, vol. 14, no. 3, pp. 1–21, May 2022.  
[35] M. Grant and S. Boyd. (Mar. 2014). CVX: MATLAB Software for Disciplined Convex Programming, Version 2.1. [Online]. Available: http://cvxr.com/cvx  
[36] M. Grant and S. Boyd, “Graph implementations for nonsmooth convex programs,” in Recent Advances in Learning and Control (Lecture Notes in Control and Information Sciences), V. Blondel, S. Boyd, and H. Kimura, Eds., Cham, Switzerland: Springer-Verlag, 2008, pp. 95–110. http://stanford.edu/\~boyd/graph\_dcp.html  
[37] S. Boyd and L. Vandenberghe, Convex Optimization. Cambridge, U.K.: Cambridge Univ. Press, 2004.

![](images/8041b05df42e3bcbf1b58ea82fe2835936001f990d81eb0bbecda282651d009c.jpg)

<details>
<summary>natural_image</summary>

Portrait of a man wearing glasses and a black shirt (no text or symbols visible)
</details>

Avik Ghosh (Graduate Student Member, IEEE) received the Bachelor of Technology degree in mechanical engineering from Indian Institute of Engineering Science and Technology, Shibpur, Howrah, India, in 2019. He is currently pursuing the Ph.D. degree with the University of California at San Diego (UCSD), San Diego, CA, USA, under the supervision of Prof. Jan Kleissl and Prof. Sonia Martínez.

He researched on solar desiccant cooling systems for hot and humid subtropical climates. His current

research focuses on applying control, probability theory, and optimization for smart charging/discharging of EVs and batteries for renewable-dominated microgrids.

![](images/5f136c52617d8700ab7461e65917fb60eb7a25f4fef19d0eddbbc27969e7c069.jpg)

<details>
<summary>natural_image</summary>

Portrait of a smiling bald man wearing a collared shirt (no text or symbols visible)
</details>

Cristian Cortes-Aguirre received the bachelor’s degree, the master’s degree from the Pontificia Universidad Catolica de Chile, Santiago, Chile, in 2012, and the Ph.D. degree from the Department of Mechanical and Aerospace Engineering, University of California at San Diego (UCSD), San Diego, CA, USA, in 2024, under the supervision of Prof. Jan Kleissl.

His research interests include solar resource assessment, solar forecasting, and distributed optimization and control of distributed energy resources (DERs). His current research focuses on distributed optimization for voltage regulation using reactive power from solar inverters and demand charge reduction through optimal storage system dispatch.

![](images/35996e237736766ec42e2a770c9b7b3de5da2b41072977288a2c8a1ce14d62ae.jpg)

<details>
<summary>natural_image</summary>

Portrait photo of a woman with long dark hair (no text or symbols visible)
</details>

Yi-An Chen is currently pursuing the Ph.D. degree with the Center for Energy Research, University of California at San Diego (UCSD), San Diego, CA, USA, specializing in control and optimization for microgrids.

She has collaborated with various academic teams, industry partners, and energy companies, contributing to both theoretical advancements and practical applications in energy markets. Her research focuses on convex optimization for energy storage systems and aggregated electric vehicle flexibility consid-

ering demand charge management and energy market participation under forecast uncertainty.

![](images/2b9d72dba02b3967ec3c7b43550f3425cdb1e655a518be640099f283718517ed.jpg)

<details>
<summary>natural_image</summary>

Portrait of a man with mustache wearing a striped shirt (no text or symbols visible)
</details>

Adil Khurram (Member, IEEE) received the bachelor’s degree from the National University of Sciences and Technology, Islamabad, Pakistan, in 2013, and the master’s degree from American University of Sharjah, Sharjah, United Arab Emirates, in 2016, both in electrical engineering, and the Ph.D. degree in electrical engineering from the University of Vermont (UVM), Burlington, VT, USA, in 2021, under the supervision of Prof. Mads Almassalkhi and Prof. Luis Duffaut Espinosa.

He is currently an Assistant Project Scientist work-

ing under the supervision of Prof. Jan Kleissl on the NSF funded project DERConnect which is a national testbed, under development, for distributed controls in power and energy systems. .

![](images/d33498e1ffb17000b936a3b535c7717828e95aa8b94b62006e67cae5feaa1aae.jpg)

<details>
<summary>natural_image</summary>

Portrait of a man with dark curly hair wearing a light-colored checkered shirt (no text or symbols visible)
</details>

Jan Kleissl received the bachelor’s degree from the University of Stuttgart, Stuttgart, Germany, in 2000, in environmental engineering with a focus in environmental fluid mechanics, the master’s degree in 2001, and the Ph.D. degree from the Johns Hopkins University, Baltimore, MD, USA, in 2024, in environmental engineering with a focus in environmental fluid mechanics.

He is currently a Professor with the Department of Mechanical and Aerospace Engineering, University of California at San Diego (UCSD), San Diego, CA,

USA, where he is also the Director of the Center for Energy Research. He is the Primary Investigator (PI) of DERConnect, an NSF-funded testbed being built to allow universities, utilities, and industry leaders to achieve a better understanding of how to integrate renewable energy resources into the power grid. His research focuses on the interaction of weather with engineering systems in solar power systems and the electric power grid.