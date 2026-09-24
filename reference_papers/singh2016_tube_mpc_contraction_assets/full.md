# Tube-Based MPC: a Contraction Theory Approach

Sumeet Singh Marco Pavone Jean-Jacques E. Slotine

Abstract— The objective of this paper is to devise a systematic approach to apply the tube MPC framework to non-linear continuous-time systems. In tube MPC, an ancillary feedback controller is designed to keep the actual state within an invariant “tube” around a nominal trajectory computed neglecting disturbances. Our approach is to leverage recent results in contraction theory together with tools from convex optimization to devise ancillary feedback controllers that (a) enjoy quantifiable bounds for the state tube, (b) provide exponential convergence, and (c) fully exploit the nonlinearity of a system, thereby minimizing conservatism. We present a number of methods to design contraction-based ancillary feedback controllers, along with numerical results corroborating our analytical insights.

## I. INTRODUCTION

Model Predictive Control (MPC) is one of the most popular methods to solve optimal control problems in an online setting [1]–[3], owing to its ability to explicitly account for complex state/control constraints while optimizing performance criteria. The key idea behind MPC is to obtain the control action by repeatedly solving, at each sampling instant, a finite horizon open-loop optimal control problem using the current state of the system as the initial state. The result of the optimization is an open-loop control sequence whose first portion is applied to control the system, following which, the algorithm repeats [4]. The solution to the standard MPC optimization problem provides a predicted optimal state trajectory and a corresponding open-loop control sequence for a finite time interval, under the assumption that the disturbances are equal to zero. Since most systems are indeed subject to modeling uncertainty and exogenous disturbances, the actual state trajectory will deviate from the one predicted. If not controlled, such deviation may grow unboundedly and cause the system to violate performance and stability constraints. This highlights the need to robustify the nominal MPC controller, specifically, the need to design a feedback control policy, rather than an open-loop control sequence [3], [5].

Most of the existing literature on the topic of robust MPC may be broadly categorized into two branches: (1) min-max formulations, and (2) tube MPC approaches [6]– [8]. In the min-max formulation, open-loop performance is optimized assuming a worst-case disturbance input sampled from a bounded set [8]. This leads to extremely conservative control policies and a small domain of feasibility. Alternatively, closed-loop min-max formulations (usually termed “feedback MPC”), while possessing improved feasibility properties, are prohibitively complex to implement as the optimization involves searching over control policies – an infinite-dimensional problem [9]. In the tube MPC approach, an ancillary feedback controller that acts on the deviation between the states is used to keep the actual state within an invariant “tube” around the nominal trajectory computed by solving the nominal MPC problem [10]–[12]. The advantages of a tube-based approach are twofold. First, the online optimization problem is converted back into a search over control sequences rather than control policies, thereby reducing computational complexity. Second, the ancillary controller ensures that even in the presence of uncertain dynamics and bounded exogenous disturbances, the deviation remains bounded. Accordingly, in this paper we will consider a tube MPC approach to robustify the MPC control of a nonlinear continuous-time system.

While tube MPC has been studied extensively for linear dynamics [12], the construction of invariant tubes and the design of the associated ancillary controller in the nonlinear setup is significantly more complicated than in the linear case. Some techniques proposed in existing literature include integral sliding mode control [13]; ellipsoidal invariant tubes constructed using linear-matrix-inequalities (LMI) and bounds on the Lipschitz constant [14] or assuming a polytopic linear differential inclusion model for the dynamics [15]; systems with matched non-linearities [16]; and dual-MPC [17]. In the last example, the idea is to employ two parallel MPC algorithms; the first algorithm generates a nominal state and control trajectory assuming no disturbances. The second MPC algorithm acts as an effective ancillary controller by penalizing deviations from the nominal trajectory computed within the first algorithm. More recently, the concept of incremental input-to-state stability (δ − ISS) for discrete-time systems was used in [18] to derive the invariant tube as a sublevel set of the associated δ − ISS Lyapunov function.

In comparison with existing literature on tube MPC algorithms, our method boasts several key advantages. First, it yields a direct, tangible measure of the “size” of the invariant state tube around the nominal trajectory (in other approaches, e.g., [17], this is instead often difficult to quantify). Second, in case of vanishing disturbances, our approach guarantees exponential stabilizability (see Definition IV.1) between the actual and nominal state trajectories by design, as opposed to only asymptotic guarantees in [17]. Third, our construction of the invariant tube is based on analyzing the intrinsic nonlinear properties of the system as opposed to relying on Lipschitz bounds as in [14], [17], or assuming conservative polytopic bounds on the dynamics’ Jacobians as in [15] – as such, our method is less conservative. Finally, with regards to the work in [18] which most closely resembles the spirit of this paper, namely the use of δ−ISS to derive invariant tubes, our approach simultaneously allows for both the design and the optimization of the invariant tubes, rather than assuming the existence of a suitable δ−ISS Lyapunov function.

Organization: The remainder of the paper is organized as follows. In Section II we define the notation used in this paper. In Section III we rigorously formulate the problem we address in this paper and state our objectives. In Section IV we review some results from contraction theory that our instrumental to our approach. In Section V we present a number of methods to design contraction-based ancillary feedback controllers. In Section VI we discuss the practical implementation of our algorithms from a computational standpoint. In Section VII we present results from numerical experiments and, finally, in Section VIII we draw our conclusions and provide directions for future research.

## II. NOTATION

Let $\mathbb { S } _ { i } ^ { + }$ be the set of symmetric positive definite matrices and $\mathbb { S } _ { j }$ the set of symmetric positive semidefinite matrices in $\mathbb { R } ^ { j \times j }$ . Given a matrix X , let ${ \widehat { X } } : = X + X ^ { T }$ . The set of twice differentiable functions from a space D to R is denoted by $\mathcal { C } ^ { 2 } ( \mathcal { D } , \mathcal { R } )$ . We denote the components of a vector $\boldsymbol { y } \in \mathbb { R } ^ { \bar { n } }$ as $y [ j ] , \ j = 1 , \ldots , n$ , and its Euclidean norm as $\| y \|$ . Let $\| y \| _ { A } = \sqrt { y ^ { T } A y }$ denote a weighted norm for $A \in \mathbb { R } ^ { n \times n }$ Let ${ \overline { { \sigma } } } ( A )$ denote the maximum singular value of a matrix A and $\partial { \dot { \boldsymbol { y } } } ^ { \aa } { \dot { F } } ( { \boldsymbol { x } } )$ the directional derivative of the matrix valued function F at x along the vector y. Given sets A and B, the operation $A \sim B$ denotes the Pontryagin set difference, and ${ \bar { A } } \oplus B .$ , the Minkowski sum.

## III. PROBLEM FORMULATION

Consider the nonlinear, time-invariant, control-affine dynamics model given by

$$
\dot {x} (t) = f (x (t)) + B (x (t)) u (t),\tag{1}
$$

where $\ b { x } ( t ) \in \mathbb { R } ^ { n }$ is the state, $u ( t ) \in \mathbb { R } ^ { m }$ is the control input, t denotes time, and $f ( x )$ and $\dot { B } ( x )$ are nonlinear, continuously differentiable vector- and matrix-valued functions. We write $B ( x )$ in column form as $[ b _ { 1 } ( x ) , b _ { 2 } ( x ) , \dots , b _ { m } ( x ) ]$ A state-input trajectory satisfying the above dynamics is denoted as a pair $( x , u )$ . We assume that $f ( 0 ) { } = 0$ , and thus $( x , u ) = ( 0 , 0 )$ is an equilibrium point for the dynamics in (1).

The perturbed dynamics model is given by

$$
\dot {x} (t) = f (x (t)) + B (x (t)) u (t) + B _ {w} w (t),\tag{2}
$$

where $w ( t ) \in \mathbb { R } ^ { n _ { w } }$ is a disturbance input and $B _ { w } \in \mathbb { R } ^ { n \times n _ { w } }$ is a constant matrix with $\bar { \sigma } ( B _ { w } ) = 1$ (in other words, $B _ { w }$ simply selects the channels where the disturbance is active). The ideal problem we wish to solve is stated as follows:

Optimization Problem OPT <sub>∞</sub> — Solve

min π∈Π(X ,U)

$$
J (x (t), \pi (x (t)))\tag{3}
$$

subject to

Perturbed dynamics: eq. (2)

(4)

$$
\text { Initial   condition: } \quad x (0) = x _ {0}\tag{5}
$$

$$
\text { Disturbances: } \quad w (t) \in \mathcal {W}, \forall t \geq 0\tag{6}
$$

$$
\text { State   constraints: } \quad x (t) \in \mathcal {X}, \forall t \geq 0\tag{7}
$$

$$
\text { Control   constraints: } \quad \pi (x) \in \mathcal {U}, \forall x \in \mathcal {X}\tag{8}
$$

where $x _ { 0 }$ is the initial state and $\pi ( \mathcal { X } , \mathcal { U } )$ denotes the set of control policies mapping states in X to the allowable control inputs in U. The cost function J is defined as:

$$
J (x (t), \pi (x (t))) := \int_ {0} ^ {\infty} \phi (x (t), \pi (x (t))) d t.\tag{9}
$$

We consider the following assumptions about problem $\mathcal { O P T } _ { \infty }$

Assumption 1 (State, Control, and Disturbance Sets). The sets $\bar { \mathcal { X } , \mathcal { U } }$ and W are closed, bounded, and connected subsets $o f \mathbb { R } ^ { n } , \mathbb { R } ^ { m }$ , and $\mathbb { R } ^ { n _ { w } }$ respectively, with the origin contained in their interior.

Assumption 2 (Cost Function). The incremental cost function $\bar { \phi ( \cdot , \cdot ) }$ is quadratic, i.e.,

$$
\phi (x, u) = x ^ {T} Q x + u ^ {T} R u,\tag{10}
$$

where $Q \in \mathbb { S } _ { n }$ and $R \in \mathbb { S } _ { m } ^ { + }$

Note that problem $\mathcal { O P T } _ { \infty }$ involves an optimization with respect to closed-loop policies, given that the dynamics are perturbed by a disturbance. As such, problem $\mathcal { O P T } _ { \infty }$ is extremely difficult to solve, thereby motivating the use of a robust receding-horizon approach. A tractable formulation of robust MPC is represented by the tube MPC framework, discussed next.

## A. Robust Tube MPC

The basic idea of the tube MPC approach is to decompose the computation of a receding-horizon control law into (a) a deterministic MPC problem which uses the nominal dynamics model in (1) to compute a desired state and control trajectory pair $( x ^ { * } ( t ) , u ^ { * } ( { \bar { t } } ) )$ over a finite time horizon $[ t , t + \bar { T } ]$ ], and (b) a feedback control problem that provides a control policy to keep the actual state x(t) close to $x ^ { * } ( t )$ For tractability, we parameterize the net control u(t) as

$$
u (t) = u ^ {*} (t) + k \left(x ^ {*} (t), x (t)\right),\tag{11}
$$

where $k ( x ^ { * } ( t ) , x ( t ) )$ is referred to as the ancillary control law, designed such that $x ( t )$ remains within a robust control invariant set (defined below) centered on $x ^ { * } ( t )$

Definition III.1 (Robust Control Invariant Set). Define $e ( t ) : = x ( t ) - x ^ { \ast } ( t )$ . A set $\Omega \subset \mathbb { R } ^ { n }$ is termed a robust control invariant (RCI) set if there exists an ancillary feedback control law $k ( x ^ { * } , x )$ such that: (a) $\forall e \in \Omega , k ( x ^ { \ast } , \bar { x } ) + \bar { u } \in$ U, $\forall \bar { u } \in \bar { u } ,$ and (b) $i f e ( t _ { 0 } ) \in \Omega ,$ , then for all allowable realizations of the disturbance $w ( t ) \in \mathcal { W } , \ e ( t ) \in \Omega$ for all $t \geq t _ { 0 } .$

Accordingly, the nominal MPC problem solved at the discrete time instant $t _ { i } , ~ i \in \mathbb { N } \ \mathrm { i s }$

Optimization Problem MPC<sub>nominal</sub> — Solve

$$
\min _ {\bar {u} (t) \in \hat {\mathcal {C}} ([ t _ {i}, t _ {i} + T ], \mathcal {U})} J _ {T} (\bar {x}, \bar {u})
$$

$$
\mathrm{subject~to}
$$

$$
\bar {x} (t _ {i}) = \bar {x} _ {0}
$$

$$
\dot {\bar {x}} = f (\bar {x} (\tau)) + B (\bar {x}) \bar {u} (\tau), \forall \tau \in [ t _ {i}, t _ {i} + T ]
$$

$$
\bar {x} (\tau) \in \bar {\mathcal {X}}, \quad \forall \tau \in [ t _ {i}, t _ {i} + T ]
$$

$$
\bar {u} (\tau) \in \bar {\mathcal {U}}, \quad \forall \tau \in [ t _ {i}, t _ {i} + T ]
$$

$$
\bar {x} (t _ {i} + T) \in \mathcal {X} _ {f},
$$

where $\hat { \mathcal { C } } ( [ t _ { i } , t _ { i } + T ] , \mathcal { U } )$ is the set of piecewise continuous functions from $[ t _ { i } , t _ { i } { + } T ]$ ] to $\bar { \mathcal { U } } , \mathcal { X } _ { f } \subset \bar { \mathcal { X } }$ is a terminal set, $\bar { \mathcal { X } } : = \dot { \mathcal { X } } \sim \Omega$ is the tightened state constraint, and U<sup>¯</sup> is the tightened control constraint, defined as $\bar { \mathcal { U } } \ : = \ \{ \bar { u } ^ { \bar { } } \in \mathcal { U } \ : \ \bar { u } \ +$ $k ( x ^ { * } ( t ) , x ( t ) ) \in \mathcal { U } , \ \forall e ( t ) \ \in \ \mathsf { \backslash } \ \mathrm { ~ \{ ~ \} ~ - ~ f o r ~ a ~ \ g i v e n }$ 1 ancillary feedback control law. The finite horizon, nominal cost function $J _ { T }$ is defined as:

$$
J _ {T} = \int_ {t _ {i}} ^ {t _ {i} + T} \phi (\bar {x} (\tau), \bar {u} (\tau)) d \tau + \Psi (\bar {x} (t + T)),\tag{12}
$$

where $\Psi ( \cdot )$ is a terminal cost function.

The choice of the initial state for problem $\mathbf { M P C } _ { \mathrm { n o m i n a l } } .$ , i.e., $\bar { x } _ { 0 }$ is discussed further on. The control trajectory solution to problem $\mathbf { M P C } _ { \mathrm { n o m i n a l } }$ is denoted as $u _ { T } ^ { * } ( t ; \bar { x } ( t _ { i } ) )$ and the nominal associated state trajectory is denoted as $x _ { T } ^ { * } ( t ; \bar { x } ( t _ { i } ) )$ 1 for $t \in [ t _ { i } , t _ { i } + T ]$ ]. Problem $\mathbf { M P \dot { C } } _ { \mathrm { n o m i n a l } }$ is solved at regular time intervals of length δ. The net control implemented within the time interval $[ t _ { i } , t _ { i } + \delta )$ is then given as:

$$
u (t) = u _ {T} ^ {*} (t; \bar {x} (t _ {i})) + k (x _ {T} ^ {*} (t; \bar {x} (t _ {i})), x (t)), t \in [ t _ {i}, t _ {i} + \delta).\tag{13}
$$

This defines the sampled MPC strategy commonly employed for continuous time systems. The next subsection addresses the recursive feasibility and closed-loop stability properties under the tube-MPC framework.

## B. Recursive Feasibility and Closed-Loop Stability

The following definition provides a notion of stability for systems with non-vanishing disturbances.

Definition III.2 (Ultimate Boundedness). The system in (2) is asymptotically ultimately bounded if there exists a control law such that the closed-loop system converges asymptotically to a bounded set.

In order to ensure recursive feasibility and closed-loop stability within the tube-MPC framework, we make the following assumptions:

Assumption 3 (Terminal Constraint Set). There exists a feasible control law $k _ { f } ( \bar { x } )$ that renders the terminal set $\chi _ { f }$ control invariant for the nominal dynamics. That is, $\dot { k _ { f } } ( \bar { x } ) \in \bar { \mathcal { U } }$ for all $\bar { x } \in \mathcal { X } _ { f }$ , and $i f \ \bar { x } ( t _ { 0 } ) \ \in \ \mathcal { X } _ { f }$ , then the solution to (1) with $\bar { u } = k _ { f } ( \bar { x } )$ ensures that $\bar { x } \in \mathcal { X } _ { f }$ for all $t \geq t _ { 0 }$

Assumption 4 (Terminal Set Stability). The terminal cost function Ψ(¯x) is a continuous positive definite function such that

$$
\frac {\partial \Psi (\bar {x})}{\partial \bar {x}} (f (\bar {x}) + B (\bar {x}) k _ {f} (\bar {x})) \leq - \phi (\bar {x}, k _ {f} (\bar {x})), \forall \bar {x} \in \mathcal {X} _ {f}.
$$

That is, $\Psi ( \bar { x } )$ is a Lyapunov function inside the terminal set $\chi _ { f } f o r$ the nominal closed-loop dynamics under the terminal control law $k _ { f } ( \bar { x } )$ .

Assumption 5. The set Ω is a RCI set under the action of the ancillary feedback law $k ( x ^ { * } ( t ) , x ( t ) )$ ).

Given the assumptions above, one can prove recursive feasibility and closed-loop stability for the closed-loop dynamics:

Theorem III.3 (Recursive Feasibility and Closed-Loop Stability). Consider the perturbed system dynamics in (2). Suppose Assumptions 1, 2, 3, 4, 5 are satisfied and problem $M P C _ { n o m i n a l }$ is feasible at time $t = 0$ . Then under the control law in (13):

1) Problem $M P C _ { n o m i n a l }$ is recursively feasible at all solve times $t _ { i } , i > 0$

2) The system is asymptotically ultimately bounded and the state trajectory converges to the RCI set Ω centered at the origin.

Proof. See for e.g. [19].

Remark III.4 (Initial State for $\mathbf { M P C } _ { \mathrm { n o m i n a l } } )$ . The initial state for problem MPC<sub>nominal</sub>, $i . e . , \bar { x } _ { 0 } ,$ , at time $t _ { i + 1 }$ may be chosen as $\bar { x } _ { T } ^ { * } ( t _ { i + 1 } ; \bar { x } ( t _ { i } ) )$ computed at the preceding time step. In this paper, we adopt the improved strategy in [20], where the initial state is also an optimization variable subject to the following additional constraint:

$$
x (t _ {i + 1}) \in \bar {x} _ {0} \oplus \Omega .\tag{14}
$$

Through application of the ancillary feedback and Assumption 5, we are guaranteed that $x ( t _ { i + 1 } ) \in x _ { T } ^ { * } ( t _ { i + 1 } ; \bar { x } ( t _ { i } ) ) \oplus \bar { \Omega } ,$ and consequently, recursive feasibility is unaffected. The above relaxation then simply allows for a better nominal trajectory to be computed at time $t _ { i + 1 }$ while ensuring that the true state remains within the RCI set Ω centered on $\bar { x } _ { 0 } .$

## C. Objective of the Paper

The objective of this paper is to leverage contractiontheoretical techniques to design an ancillary feedback law such that (a) convergence to the nominal MPC trajectory is exponential, (b) the associated RCI set is as small as possible, and (c) the net control effort is as small as possible.

We start with an overview of some concepts from contraction theory that will be instrumental to design of an ancillary feedback controller.

## IV. CONTROL CONTRACTION METRICS AND DIFFERENTIAL CONTROLLERS

In this section we review some definitions and properties about incremental exponential stability, control contraction metrics, and differential controllers. Note that all statecontrol trajectories referred to within this section are assumed to be solutions to the nominal dynamics in (1).

## A. Incremental Exponential Stability

Incremental exponential stability is defined as,

Definition IV.1 (Incremental Exponential Stability). Consider a nominal state/control trajectory pair $( x ^ { * } ( t ) , u ^ { * } ( t ) )$ Suppose there exist positive constants λ and $\dot { C }$ and a feedback controller of the form $u ^ { * } ( t ) + k ( x ^ { * } ( t ) , x ( t ) )$ such that

$$
\left\| x ^ {*} (t) - x (t) \right\| \leq C e ^ {- \lambda t} \kappa \left(\left\| x ^ {*} (0) - x (0) \right\|\right),\tag{15}
$$

where $\kappa \in { \mathcal { K } } ^ { \infty }$ and $( x , u ^ { * } + k )$ is a state/control solution trajectory for the dynamics (1). Then, the state trajectory $x ^ { * } ( t )$ is said incrementally exponentially stabilizable (IES) with rate λ. Furthermore, if inequality (15) holds uniformly (i.e., with same $\kappa ( \cdot ) , \lambda ,$ and C) for all nominal state/control trajectory pairs $( \dot { x ^ { * } } ( t ) , u ^ { * } ( t ) )$ , then the dynamical system (1) is said to be uniformly incrementally exponentially stabiliz able (UIES) with rate λ.

The goal of the feedback controller $k ( \cdot )$ , referred to as the ancillary feedback controller within the tube MPC framework, is to stabilize a nominal trajectory $x ^ { * } ( t )$ obtained from the solution to a nominal MPC problem where disturbances are neglected.

## B. Control Contraction Metrics

Denote the tangent space of X at $x \in \mathcal { X }$ by $T _ { x } \mathcal { X }$ and the tangent bundle of X by $\begin{array} { r } { T \mathcal { X } \ = \ \bigcup _ { x \in \mathcal { X } } \{ \overset { } { x } \} \ \times \ T _ { x } \mathcal { X } } \end{array}$ The variational dynamics for the dynamical system (1) are defined (over $T \dot { \mathcal { X } } )$ as

$$
\dot {\delta} _ {x} = \underbrace {\left(\frac {\partial f (x)}{\partial x} + \sum_ {j = 1} ^ {m} u [ j ] \frac {\partial b _ {j} (x)}{\partial x}\right) \Bigg | _ {(x , u)}} _ {:= A (x, u)} \delta_ {x} + B (x) \delta_ {u},\tag{16}
$$

where $\delta _ { x } ~ \in ~ T { \mathcal { X } }$ is a tangent vector to a smooth path of states at $x \in \mathcal { X }$ , and $\delta _ { u } ~ \in ~ T \mathcal { U }$ is a tangent vector to a smooth path of controls at $u \in \mathcal { U }$ . Let $\check { M ( } x ) : \mathbb { R } ^ { n }  \mathbb { S } _ { n } ^ { + }$ be a continuous symmetric positive definite matrix function that is uniformly bounded $( \mathrm { i . e . } ,$ , there exist constants $0 ~ <$ $\underline { { \alpha } } ~ < ~ \overline { { \alpha } }$ such that $\underline { { { \alpha } } } I \preceq M ( x ) \preceq \overline { { { \alpha } } } I )$ . The inner product $V ( x , \delta _ { x } ) : = \delta _ { x } ^ { T } M ( x ) \delta _ { x }$ on T X defines a Riemannian metric on $T \mathcal { X }$ according to the metric tensor $M ( x )$ , and represents an infinitesimal measure of length on $\dot { T } \dot { \mathcal { X } }$ . For a given smooth curve $c : [ 0 , 1 ] \to \mathcal { X }$ , we define its length $l ( c )$ and energy $\mathcal { E } ( c )$ as:

$$
l (c) := \int_ {0} ^ {1} \sqrt {V (c (s) , c _ {s} (s))} d s, \mathcal {E} (c) := \int_ {0} ^ {1} V (c (s), c _ {s} (s)) d s
$$

where $c _ { s } ( s ) = \partial c ( s ) / \partial s$ . Let $\Gamma ( p , q )$ be the set of smooth curves on X that connect the points p and $q , \ \mathrm { i . e . }$ , for all $c ~ \in ~ \Gamma ( p , q ) , ~ c ( 0 ) ~ = ~ p$ and $c ( 1 ) \ = \ q$ . Let $d ( p , q ) : =$ $\operatorname* { i n f } _ { c \in \Gamma ( p , q ) ) } l ( c )$ and let the geodesic be a curve $\gamma \in \Gamma ( p , q )$ that achieves this infimum (which, by the Hopf-Rinow theorem, is guaranteed to exist and lie entirely in X since we assume X to be a closed, connected, compact subspace in $\mathbb { R } ^ { n } )$ . Note that $d ^ { 2 } ( p , q ) = \mathcal { E } ( \gamma )$

A control contraction metric (CCM) on X is a tensor metric $M ( x )$ such that the induced Riemannian metric $V ( x , \delta _ { x } ) : = \stackrel { \textstyle \cdot } { \delta } \delta _ { x } ^ { T } M ( x ) \delta _ { x }$ shrinks at all points in X , that is $\dot { V } ( x , \delta _ { x } ) < 0$ at all $x \in \mathcal { X }$ . In other words, the inner product $V ( x , \delta _ { x } )$ acts as a differential Lyapunov function on the tangent bundle $_ { T \mathcal { X } }$ and can be used to ascertain incremental exponential stabilizability of a given nominal trajectory. Given the system dynamics in (1) and the variational system in $( 1 6 ) , \dot { V } ( x , \delta _ { x } )$ can be written as:

$$
\begin{array}{l} \dot {V} (x, \delta_ {x}) = \delta_ {x} ^ {T} \dot {M} \delta_ {x} + \delta_ {x} ^ {T} M (x) \dot {\delta} _ {x} + \dot {\delta} _ {x} ^ {T} M (x) \delta_ {x} \\ = \delta_ {x} ^ {T} \left(\partial_ {f (x) + B (x) u} M (x) \right. \\ \left. + \overbrace {\frac {\partial f (x) ^ {T}}{\partial x} M (x)} + \sum_ {j = 1} ^ {m} u [ j ] \overbrace {\frac {\partial b _ {j} (x) ^ {T}}{\partial x} M (x)}\right) \delta_ {x} \\ + 2 \delta_ {x} ^ {T} M (x) B (x) \delta_ {u}. \end{array} \tag {17}
$$

Suppose now that the following two conditions hold for some constant $\lambda > 0$ and all $( x , \bar { \delta _ { x } } ) \in T \mathcal { X }$

$$
\partial_ {b _ {j}} M (x) + \overline {{\frac {\partial b _ {j} (x) ^ {T}}{\partial x} M (x)}} = 0, \quad j = 1, \ldots , m\tag{18}
$$

$$
\delta_ {x} ^ {T} \left(\partial_ {f} M (x) + \overline {{\frac {\partial f (x) ^ {T}}{\partial x} M (x)}}\right) \delta_ {x} \leq - 2 \lambda \delta_ {x} ^ {T} M (x) \delta_ {x}\tag{19}
$$

for all $\delta _ { x }$ such that $\delta _ { x } ^ { T } M ( x ) B ( x ) = 0$

Note that the left-hand sides in (18) are the components multiplying the terms $u [ j ]$ in equation (17). Thus, geometrically, condition (18) implies that the vectors $b _ { j }$ form a Killing vector field for the metric tensor $M ( x )$ , i.e., that control cannot cause the infinitesimal distances to grow unboundedly. On the other hand, condition (19) indicates that for all directions where the system lacks controllability (given by the nullspace of $B ^ { T } \dot { ( } x ) M ( x ) \rangle$ , the system is naturally contracting with rate λ. Under conditions (18) and (19), $\dot { V ( x , \delta _ { x } ) }$ reduces to:

$$
\begin{array}{l} \dot {V} (x, \delta_ {x}) = \delta_ {x} \left(\overline {{\frac {\partial f (x) ^ {T}}{\partial x} M (x)}}\right) \delta_ {x} + 2 \delta_ {x} ^ {T} M (x) B (x) \delta_ {u} \\ \quad + \delta_ {x} ^ {T} \left(\partial_ {f} M (x)\right) \delta_ {x}, \quad \forall (x, \delta_ {x}) \in T \mathcal {X}. \end{array} \tag {20}\tag{20}
$$

Given conditions (18) and (19), [21] shows that there always exists a differential feedback controller of the form $\delta _ { u } ( x ) = K ( x ) \delta _ { x }$ such that the following inequality holds for all $( \dot { x } , \delta _ { x } ) \dot { \in { \dot { T } } } { \mathcal { X } }$

$$
\begin{array}{l} \dot {V} (x, \delta_ {x}) = \delta_ {x} ^ {T} \left(\partial_ {f} M (x) + \overline {{\frac {\partial f (x) ^ {T}}{\partial x} M (x)}} \right. \\ \qquad \qquad \qquad \qquad \qquad \qquad \qquad \qquad \qquad \qquad \qquad \qquad \qquad \qquad + \overline {{M (x) B (x) K (x)}} \Bigg) \delta_ {x} \\ \leq - 2 \lambda \delta_ {x} ^ {T} M (x) \delta_ {x} = - 2 \lambda V (x, \delta_ {x}). \end{array}\tag{21}
$$

Following the proof in [21], consider the geodesic $\gamma ( s , t ) , \quad \bar { s } ~ \in ~ [ \bar { 0 } , 1 ] .$ , connecting points $x ^ { * } ( t )$ and $x ( t )$ at time t. From the inequality above, we deduce that $\dot { V } ( \gamma ( s , t ) , \delta _ { \gamma } ( s , t ) ) ~ \le ~ - 2 \bar { V } ( \gamma ( \dot { s } , t ) , \delta _ { \gamma } ( s , t ) ) , ~ \forall s ~ \in ~ [ 0 , 1 ]$ where $\delta _ { \gamma } ( s , t ) : = \partial \gamma ( s , t ) / \partial s$ . That is, the Riemannian metric is contracting at all points along the geodesic connecting $x ^ { * } ( t )$ and $x ( t )$ . Thus, we may conclude the following:

$$
\begin{array}{l} \dot {\mathcal {E}} (\gamma (t)) = \frac {d}{d t} \int_ {0} ^ {1} V (\gamma (s, t), \delta_ {\gamma} (s, t)) d s \\ \qquad \leq - 2 \lambda \int_ {0} ^ {1} V (\gamma (s, t), \delta_ {\gamma} (s, t)) d s = - 2 \lambda \mathcal {E} (\gamma (t)). \end{array}
$$

That ${ \mathrm { i s } } ,$ the geodesic energy converges to zero exponentially at rate 2λ. Consequently, the geodesic distance $d ( \dot { x } ^ { * } ( t ) , x ( t ) )$ converges exponentially to zero at rate λ, proving incremental exponential stability with respect to the trajectory $x ^ { * } ( t )$ in the sense of Definition IV.1.

## C. Incrementally Stabilizing Controllers

As shown in [21], conditions (18) and (19) can be written in compact form as pointwise convex LMIs by introducing the dual metric $W ( \dot { x } ) : = M ( x ) ^ { - 1 }$ and the contravariant transformation $\eta _ { x } : = M ( x ) \delta _ { x }$ . Specifically, conditions (18) and (19) are equivalent to the conditions, for all $( x , \eta _ { x } ) \in$ ${ \mathbf { } } T ^ { * } { \mathbf { } } X$ , where $T ^ { * } { \mathcal { X } }$ denotes the co-tangent bundle,

$$
\partial_ {b _ {j}} W (x) - \overline {{W (x) \frac {\partial b _ {j} (x) ^ {T}}{\partial x}}} = 0, \quad j = 1, \ldots , m\tag{22}
$$

$$
\eta_ {x} ^ {T} \left(- \partial_ {f} W (x) + \overline {{W (x) \frac {\partial f (x) ^ {T}}{\partial x}}}\right) \eta_ {x} \leq - 2 \lambda \eta_ {x} ^ {T} W (x) \eta_ {x},\tag{23}
$$

for all $\eta _ { x }$ such that $\eta _ { x } ^ { T } B ( x ) = 0 .$

By applying Finsler’s theorem [22] the contraction condition (23) can be equivalently written as

$$
- \partial_ {f} W (x) + \overline {{W (x) \frac {\partial f (x) ^ {T}}{\partial x}}} - \rho (x) B (x) B ^ {T} (x) + 2 \lambda W (x) \prec 0, \tag {24}\tag{24}
$$

for all $x \in \mathcal { X }$ and where $\rho ( x )$ is a scalar multiplier. Note that condition (24) is a jointly convex LMI in $W { \dot { ( x ) } }$ and $\rho ( x )$

A key result is that the scalar multiplier $\rho ( x )$ yields a differential feedback law (referred to as the ρ−multiplier differential controller),

$$
\delta_ {u} (x) = \underbrace {- (1 / 2) \rho (x) B (x) ^ {T} M (x)} _ {:= K (x)} \delta_ {x},\tag{25}
$$

which verifies condition (21). Hence, given a desired nominal state/control trajectory pair $( x ^ { * } ( t ) , u ^ { * } \bar { ( t ) } )$ , at time t, a control law that ensures IES with respect to $x ^ { * } ( t )$ is given by

$$
\begin{array}{c} u (x (t)) = u ^ {*} (t) + \int_ {\gamma (s, t)} \delta_ {u} (\gamma (s, t), \delta_ {\gamma} (s, t)) d s \\ = u ^ {*} (t) + \underbrace {\int_ {0} ^ {1} K (\gamma (s , t)) \delta_ {\gamma} (s , t) d s} _ {= k (x ^ {*} (t), x (t))}. \end{array}\tag{26}
$$

The key idea of this paper is to use the stabilizing feedback controller $k ( \cdot )$ as an ancillary feedback controller within the tube MPC framework.

## V. CONTRACTION-BASED TUBE MPC

In this section we show how to derive an ancillary feedback controller by using contraction-theoretical tools. Specifically, in Section V-A we consider the case where one computes (offline) a CCM satisfying conditions (18) and (19), and uses as ancillary feedback controller the associated ρ−multiplier differential controller. In Section V-B we study a similar approach, where, however, the CCM is computed (offline) by minimizing a certain measure of the size of the RCI set associated with the ρ−multiplier differential controller. This approach yields, in general, significant improvements in the tracking capabilities of the tube MPC algorithm. Third, and last, in Section V-C we investigate an offline/online approach where a CCM is computed offline, but the associated differential controller is computed online. In this way, control effort can be significantly reduced.

Henceforth, $x ^ { * } ( t )$ denotes the solution to the nominal disturbance-free dynamics in (1) using the MPC control trajectory $u ^ { * } ( t )$ , while $x ( t )$ denotes the actual state trajectory, given by the solution to the perturbed dynamics subject to the control law $u ^ { * } ( t ) + k ( x ^ { * } , x )$ , where $k ( \stackrel { \cdot } { x } ^ { \ast } , x )$ is a differential controller associated to a CCM.

## A. Feasible Contraction-Based Tubes

In this section we assume that the CCM is computed by solving the LMIs (22) and (24), and the controller $k ( x ^ { * } , x )$ is the associated ρ-multiplier differential controller. The next theorem provides a characterization of the RCI set for this case.

Theorem V.1 (RCI Set). Assume there exists a CCM $M ( x )$ that satisfies conditions (22) and (24), and that there exist constants $0 < \underline { { \alpha } } < \overline { { \alpha } }$ such that $\underline { { { \alpha } } } I \preceq M ( x ) \preceq \overline { { { \alpha } } } I ,$ for all $x \in { \mathcal { X } } .$ . Then, the geodesic distance between trajectories $x ( t )$ and $x ^ { * } ( t )$ , i.e., d(x<sup>∗</sup>(t), x(t)) = inf<sub>c∈Γ(x</sub>∗<sub>(t),x(t))</sub> l(c), satisfies the differential inequality:

$$
D ^ {+} d \left(x ^ {*} (t), x (t)\right) \leq - \lambda d \left(x ^ {*} (t), x (t)\right) + \sqrt {\overline {{\alpha}}} \| w (t) \|,\tag{27}
$$

where $D ^ { + }$ denotes the upper-Dini derivative. Furthermore, if the disturbance w satisfies the bound $\begin{array} { r l } { \| \boldsymbol { w } \| \le } & { { } \bar { \boldsymbol { w } } , } \end{array}$ , then the geodesic distance<sub>√</sub> d is asymptotically upper bounded by $\sqrt { \bar { \alpha } } \bar { w } / \lambda$

Proof. At time $t ,$ consider the geodesic $\gamma ( s , t )$ connecting $x ^ { * } ( t )$ and $x ( t )$ . The energy of this geodesic $\overset { \cdot } { \mathcal { E } } ( \gamma ( s , t ) )$ is given by the following integral:

$$
\mathcal {E} (\gamma (s, t)) = \int_ {0} ^ {1} V (\gamma (s, t), \delta_ {\gamma} (s, t)) d s.
$$

We now wish to study the change in this quantity as the endpoints of the geodesic $\gamma ( s , t )$ move as dictated by the dynamics. Accordingly, define the function $\epsilon ( s , t ) \ \dot { \in }$ $\mathcal { C } ^ { 2 } \left( [ 0 , 1 ] \times \mathbb { R } _ { \ge 0 } , \mathcal { X } \right)$ that satisfies the following two properties:

$$
\dot {\epsilon} (0, t) = \dot {x} ^ {*} (t), \quad \text { and } \quad \dot {\epsilon} (1, t) := \dot {x} (t),\tag{28}
$$

where $\dot { \epsilon } ( s , t ) : = \partial \epsilon ( s , t ) / \partial t$ . Thus, $\epsilon ( 0 , t )$ and $\epsilon ( 1 , t )$ track the evolution of the state trajectories $x ^ { * } ( t )$ and x(t) respectively. Let $\Delta t \in \mathbb { R } _ { > 0 }$ be an infinitesimal length of time. The first variation of the energy due to the function $\dot { \epsilon } ( s , t ) \Delta t$ at time t is given as [23]:

$$
\begin{array}{l} \Delta \mathcal {E} (\gamma , \dot {\epsilon} \Delta t) = \Delta t \int_ {0} ^ {1} \left(\frac {\partial V (s , t)}{\partial \gamma (s , t)} + \frac {\partial}{\partial s} \left(\frac {\partial V (s , t)}{\partial \delta_ {\gamma}}\right)\right) ^ {T} \dot {\epsilon} (s, t) d s \\ \qquad + \Delta t \left[ \left(\frac {\partial V (s , t)}{\partial \delta_ {\gamma}}\right) ^ {T} \dot {\epsilon} (s, t) \right] \Bigg | _ {s = 0} ^ {s = 1}, \end{array}
$$

where we have employed the shorthand $\begin{array} { r l } { V ( s , t ) } & { { } = } \end{array}$ $V ( \gamma ( s , t ) , \delta _ { \gamma } ( s , t ) )$ . We now observe that by the Fundamental Theorem of Calculus of Variations [24], for $\gamma ( s , t )$ to be the geodesic at time $t ,$ the bracketed term within the integrand in the expression above must equal 0 for all $s \in [ 0 , 1 \breve { ] }$ and any variation $\dot { \epsilon } ( s , t ) \Delta t$ (thereby defining the Euler-Lagrange equation characterizing a geodesic). Thus, the first variation simply reduces to the two boundary terms. Dividing this remaining expression on both sides by $\Delta t ,$ taking the limit $\Delta t \to 0 ^ { \mp }$ , and leveraging the identities in (28) yields the following expression for the instantaneous rate of change of the geodesic energy:

$$
\begin{array}{r l} D ^ {+} \mathcal {E} (t) = 2 \delta_ {\gamma} ^ {T} (1, t) M (\gamma (1, t)) (f (x (t)) & + B (x (t)) u (x (t)) \\ & + B _ {w} w (t)) \\ - 2 \delta_ {\gamma} ^ {T} (0, t) M (\gamma (0, t)) (f (x ^ {*} (t)) + B (x ^ {*} (t)) u ^ {*} (t)) \end{array}
$$

where $u ( x ( t ) )$ is given by (26) using a differential controller (e.g., the ρ−multiplier controller) associated with the CCM M. Thus, by the IES property of the unperturbed system, the above expression can be bounded as:

$$
D ^ {+} \mathcal {E} (t) \leq - 2 \lambda \mathcal {E} (t) + 2 \delta_ {\gamma} ^ {T} (1, t) M (\gamma (1, t)) B _ {w} w (t).
$$

Making use of the factorization $M : = \Theta ^ { T } \Theta$ and defining $\delta _ { z } ( s , t ) : = \Theta ( \gamma ( s , t ) ) \delta _ { \gamma } ( s , t )$ , the above expression may be re-written as:

$$
D ^ {+} \mathcal {E} (t) \leq - 2 \lambda \mathcal {E} (t) + 2 \delta_ {z} ^ {T} (1, t) \Theta (\gamma (1, t)) B _ {w} w (t).
$$

□

We now recall that a geodesic is characterized by “constant speed” with respect to the metric tensor $[ 2 3 ] , { \mathrm { i . e . , } } V ( s , t ) =$ $\dot { \mathcal { E } } ( t ) = d ^ { 2 } ( x ^ { * } ( \dot { t } ) , x ( t ) )$ for all $s \in [ 0 , 1 ]$ . Thus, recognizing that $\| \delta _ { z } ( s , t ) \| = \sqrt { V ( s , t ) }$ , we conclude that $\| \delta _ { z } ( 1 , t ) \| =$ $\| \delta _ { z } ( \dot { s } , t ) \| = \ddot { d } ( x ^ { * } ( t ) , x ( t ) )$ . Using this relation and leveraging the upper-bound $\overline { { \sigma } } ( \Theta ( x ) ) \leq \sqrt { \overline { { \alpha } } }$ yields the inequality

$$
D ^ {+} \mathcal {E} (t) \leq - 2 \lambda \mathcal {E} (t) + 2 d (x ^ {*} (t), x (t)) \sqrt {\overline {{\alpha}}} \| w (t) \|.
$$

Using the relation ${ \mathcal E } ( t ) = d ^ { 2 } ( x ^ { * } ( t ) , x ( t ) )$ ) and applying the chain-rule gives the stated differential inequality, completing the proof.

Remark V.2 (Euclidean Distance Bound). Note that since $d ( x ^ { * } ( 0 ) , x ( 0 ) ) ~ = ~ 0 ,$ , by the comparison lemma [25], the geodesic distance is upper bounded by $\sqrt { \bar { \alpha } } \bar { w } / \lambda ,$ , for all time t. Hence, we may derive a bound on the Euclidean distance error, thereby defining the RCI set centered at $x ^ { * } ( t )$ , namely:

$$
\| x ^ {*} (t) - x (t) \| \leq \sqrt {\frac {\overline {{\alpha}}}{\underline {{\alpha}}}} \frac {\bar {w}}{\lambda}.\tag{29}
$$

Note that the bound in Remark V.2 is proportional to the square root of the condition number of $\bar { M } ( \bar { x } )$ (equivalently of $W ( x ) )$ and inversely proportional to the contraction rate. In the next section we propose an approach to compute a CCM (and associated differential controller) that optimizes this bound.

## B. Optimized Contraction-Based Tubes

In this section we replace the CCM feasibility problem (i.e., the LMIs (22) and (24)), with a quasiconvex optimization problem, with the goal of minimizing the size of the RCI set as measured by the bound in (29).

Define $\beta : = 1 / \overline { { \alpha } }$ and $\bar { \beta } = 1 / \underline { { { \alpha } } } \ ( \mathrm { i . e . , } \ \underline { { { \alpha } } } I \preceq M \preceq \bar { \alpha } I$ ⇔ $\beta I \preceq W \preceq \bar { \beta } I )$ . Then, the CCM optimization problem that, in theory, one would like to solve is

Optimization Problem $\mathcal { O P T } _ { C C M } - \mathrm { S o l v e }$

$$
\min_{\substack{W\in \mathcal{C}^{2}(\mathcal{X},\mathbb{S}_{n}^{+})\\ \rho \in \mathcal{C}^{2}(\mathcal{X},\mathbb{R})\\ \underline{\beta},\overline{\beta},\lambda \in \mathbb{R}_{>0}}}\qquad \frac{1}{\lambda}\sqrt{\overline{\beta} / \underline{\beta}}\tag{30}
$$

$$
\text { subject   to } \quad \text { eq.   (22),   eq.   (24) }\tag{31}
$$

$$
\beta I \preceq W (x) \preceq \overline {{\beta}} I\tag{32}
$$

where the conditions hold uniformly for all $x \in \mathcal { X }$

Unfortunately, problem ${ \mathcal { O P T } } _ { C C M }$ is non-convex and infinite-dimensional. Hence, we consider a tractable, finitedimensional, quasiconvex approximation, whereby the dual metric $W ( x )$ is parameterized as a matrix polynomial and we apply the sum-of-squares (SOS) relaxation over the bounded set $\mathcal { X }$ . Recognizing that for a fixed contraction rate $\lambda ,$ conditions (31) and (32) define a convex feasibility region for $W ( x )$ and $\rho ( x )$ , problem ${ \mathcal { O P T } } _ { C C M }$ can be approximated by the quasiconvex problem ${ \mathcal { O P T } } _ { \widehat { C C M } } .$

Optimization Problem $\mathcal { O P T } _ { \widehat { C C M } } - \mathrm { S o l v e }$

$$
\min_{\lambda \in \mathbb{R}_{>0}}\min_{\substack{W\in \mathcal{C}^{2}(\mathcal{X},\mathbb{S}_{n}^{+})\\ \rho \in \mathcal{C}^{2}(\mathcal{X},\mathbb{R})\\ \beta ,\overline{\beta}\in \mathbb{R}_{>0}}}\quad \overline{\beta} /\underline{\beta}\tag{33}
$$

$$
\text { subject   to } \quad \text { eq.   (22),   eq.   (24) }\tag{34}
$$

$$
\beta I \preceq W (x) \preceq \overline {{\beta}} I\tag{35}
$$

where the conditions hold uniformly for all $x \in \mathcal { X }$

The condition number minimization of a positive definite matrix over a closed convex set is a well studied problem in optimization literature and has been shown to be solvable by convex programming [26]. Furthermore, the constraints can be reformulated as SOS constraints by applying Positivstellensatz relaxations [27], thereby yielding an SDP. Problem ${ \mathcal { O P T } } _ { \widehat { C C M } }$ can then be solved by bisection search on λ, where for a fixed λ one has to solve an SDP.

The solution to problem ${ \mathcal { O P T } } _ { \widehat { C C M } }$ provides an optimized CCM and associated optimized ρ-multiplier differential controller that (often significantly) decreases the size of the RCI set. Note that both the optimized CCM and the optimized ρ-multiplier differential controller are computed $o f f i n e ,$ and thus do not leverage any additional information available online. In the next section we present an offline/online approach where the CCM is computed offline, but the ancillary feedback controller is computed online, with the objective of minimizing control effort.

## C. Offline/Online Tube MPC via Contraction Theory

To minimize the suboptimality gap of the tube MPC approach, one desires to minimize $\| u ^ { * } + k ( x ^ { * } , x ) \| _ { R } ^ { 2 } \left( \mathrm { i . e . } \right.$ , the control effort), where $k ( x ^ { * } , x )$ , as usual, denotes the ancillary feedback controller. To this purpose, consider the inequality:

$$
\begin{array}{r l} \| u ^ {*} + k (x ^ {*}, x) \| _ {R} ^ {2} & = \| u ^ {*} \| _ {R} ^ {2} + 2 u ^ {* T} R   k (x ^ {*}, x) + \| k (x ^ {*}, x) \| _ {R} ^ {2} \\ & \leq \| u ^ {*} \| _ {R} ^ {2} + 2 \| R u ^ {*} \| \| k (x ^ {*}, x) \| \\ & \quad + \| R \| \| k (x ^ {*}, x) \| ^ {2} \end{array} \tag {36}
$$

Hence, by minimizing $\| k ( x ^ { * } , x ) \|$ , one can approximately minimize the control effort. In this section we consider an offline/online approach where the (dual) CCM metric is computed offline by solving problem ${ \mathcal { O P T } } _ { \widehat { C C M } }$ , while the ancillary controller is computed online by minimizing its norm subject to the pointwise inequality constraint given in (21). Specifically, at each time t, the ancillary controller is computed as a solution to the (infinite-dimensional) convex optimization problem $\mathcal { O P T } _ { \mathrm { o n l i n e } }$

Optimization Problem $O \mathcal { P T } _ { \mathrm { o n l i n e } } - \mathrm { A t }$ time $t \geq 0 ,$ , given a dual CCM $W ( x )$ , a desired state $x ^ { * } ( t )$ , the current state $x ( t )$ , and the geodesic $\gamma ( s , t )$ connecting these two states $( \mathrm { i . e . , ~ } \gamma ( 0 , \dot { t } ) ^ { \dot { } } = x ^ { \ast } ( t )$ and $\gamma ( 1 , t ) \dot { } = x ( t ) \dot { }$ ), let $W ( s , t ) : = W ( \gamma ( s , \tilde { t ) } )$ , and $\eta _ { x } ( s , t ) : = M ( s , t ) \bar { \delta } _ { \gamma } ( s , t )$ . Solve

$$
\begin{array}{l l} \delta_ {u} ^ {*} = \underset {\delta_ {u} \in \mathcal {C} ^ {2} ([ 0, 1 ], \mathbb {R} ^ {m})} {\operatorname{argmin}} & \left\| \int_ {0} ^ {1} \delta_ {u} (s, t) d s \right\| ^ {2} \\ \text {subject to} & \eta_ {x} (s, t) ^ {T} \left(- \partial_ {f (x)} W (x) + \overbrace {W (x) \frac {\partial f (x) ^ {T}}{\partial x}}\right) \Bigg | _ {x = \gamma (s, t)} \eta_ {x} (s, t) + 2 \eta_ {x} (s, t) ^ {T} B (\gamma (s, t)) \delta_ {u} (s, t) \\ & \leq - 2 \lambda \eta_ {x} (s, t) ^ {T} W (s, t) \eta (s, t), \forall s \in [ 0, 1 ]. \end{array}\tag{37}
$$

The ancillary controller is then given by $k ( x ^ { * } ( t ) , x ( t ) ) =$ $\textstyle \int _ { 0 } ^ { 1 } { \delta _ { u } ^ { * } ( s , t ) d s }$ (henceforth, we will refer to such a controller as the “optimized differential controller”).

A few comments are in order. First, the existence of the dual metric $W ( x )$ (and, thus, the existence of the associated ρ−multiplier differential controller) ensures that problem $\mathcal { O P T } _ { \mathrm { o n l i n e } }$ is always feasible. Second, the linear inequality (37), written using the dual metric $W ( x )$ , is essentially a relaxation of the contraction inequality in (21), in that it only enforces contraction tangent to the given geodesic. (In contrast, the ρ−multiplier differential controller ensures that the system contracts in all directions with at least rate λ.) Such a relaxation still guarantees incremental exponential stabilizability, as only the flow along the geodesic affects the convergence of $x ( t )$ to $x ^ { * } ( t )$ . On the other hand, such a relaxation can often dramatically decrease control effort, as investigated via numerical experiments in SectionVII.

Problem $\mathcal { O P T } _ { \mathrm { o n l i n e } }$ is convex, yet infinite dimensional. Furthermore, it requires the online computation of the geodesic $\gamma ( s , t )$ . In the next section we present efficient numerical methods to enable the real-time solution to problem $\mathcal { O P T } _ { \mathrm { o n l i n e } } .$

## VI. NUMERICAL IMPLEMENTATION

In this section we discuss numerical methods for the online computation of the geodesic and the solution to problem $\mathcal { O P T } _ { \mathrm { o n l i n e } }$

## A. Geodesic Computation

Computation of the geodesic between two points $p , q \in$ $\mathcal { X }$ can be framed as the following functional optimization problem:

Optimization Problem ${ \mathcal { O P T } } _ { \gamma } - \mathrm { A t }$ time $t \geq 0 ,$ given desired state $x ^ { * } ( t )$ and current state $x ( t )$ solve

$$
\min _ {c (s, t) \in \Gamma (x ^ {*} (t), x (t))} \quad \int_ {0} ^ {1} c _ {s} ^ {T} (s, t) M (c (s, t)) c _ {s} (s, t) d s \tag {28}\tag{38}
$$

$$
\text { where } c _ {s} (s, t) = \partial c (s, t) / \partial s.
$$

Following the approach in [28], such a problem can be efficiently solved by applying the Chebyshev global pseudospectral method, i.e., by discretizing the interval [0, 1] using the Chebyshev-Gauss-Lobatto (CGL) nodes and using Chebyshev interpolating polynomials up to degree N to approximate the solution. The integral in (38) is approximated using the Clenshaw-Curtis quadrature (CCQ) scheme with $K > N$ nodes. As in [28], we choose $K > N$ since the integral involves the inverse of the dual metric W and is not guaranteed to be polynomial.

## B. Solution to $\mathcal { O P T } _ { o n l i n e }$

Given the solution to the geodesic problem ${ \mathcal { O P T } } _ { \gamma } ,$ parameterized by a set of values $\bar { \{ \gamma ( s _ { k } ) \} } _ { k = 0 } ^ { K }$ and $\bar { \{ \delta _ { \gamma } ( s _ { k } ) \} _ { k = 0 } ^ { K } } , s _ { k } \in [ 0 , 1 ]$ , problem $\mathcal { O P T } _ { \mathrm { o n l i n e } }$ is solved by applying again the Chebyshev pseudospectral method. In this case, however, Lagrange polynomials are used for interpolation. In fact, as these polynomials satisfy the isolation property with respect to the CGL nodes [29], the discretized version of problem $\mathcal { O P T } _ { \gamma }$ can be transformed into a quadratic program (QP):

$$
\min \qquad \pmb {\delta} _ {u} ^ {T} F \pmb {\delta} _ {u}\tag{39}
$$

$$
\text { subject   to } \quad A \delta_ {u} \leq b\tag{40}
$$

where $\boldsymbol { F } \in \mathbb { S } _ { ( K + 1 ) m }$ and $\pmb { \delta } _ { u } \in \mathbb { R } ^ { ( K + 1 ) m }$ is the stacked vector of all the $\delta _ { u } ( \dot { s } _ { k } ) , k = 0 , \dots , K$ . The integral yielding $k ( x ^ { * } , x )$ is computed using the aforementioned CCQ scheme.

## VII. SIMULATIONS

## A. Dynamics Model & Constraints

In this section we investigate the performance of “contraction-based” tube MPC. Consider the following second-order nonlinear dynamics system (taken from [14]):

$$
\dot {x} (t) = \left[ \begin{array}{c c} - 1 & 2 \\ - 3 & 4 \end{array} \right] x + \left[ \begin{array}{c} 0 \\ - 0. 2 5 x _ {2} ^ {3} \end{array} \right] + \left[ \begin{array}{c} 0. 5 \\ - 2 \end{array} \right] u (t) + \left[ \begin{array}{c} 0 \\ 1 \end{array} \right]\tag{\(w(t)\}
$$

(41)

The control constraints are:

$$
- 2 \leq u (t) \leq 2 \quad \text {   for   all   } t \geq 0.
$$

The state constraints, while not explicitly given in [14], are taken to be the $[ - 5 , 5 ] ^ { 2 }$ box. The disturbance $w ( t )$ is norm bounded, namely $\cdot \| w \| \leq 0 . 1$ , and the state and control cost matrices are $Q = \mathrm { d i a g } ( 0 . 5 , 0 . 5 )$ and $R = 1$

In the following, we first solve the quasi-convex problem ${ \mathcal { O P T } } _ { \widehat { C C M } }$ to obtain a feasible dual metric $W \bar { ( \boldsymbol { x } ) }$ and the associated ρ−multiplier differential controller. We then compare the optimized $( \mathrm { i . e . }$ , obtained by solving $\mathcal { O P T } _ { \mathrm { o n l i n e } } )$ and ρ−multiplier differential controllers in an unconstrained scenario as they attempt to regulate the state from (3, 3) to the origin.

## B. CCM Synthesis and RCI set

To derive the CCM and associated ρ−multiplier differential controller, we solved problem $\mathcal { O } \mathcal { \dot { P } T } _ { \widehat { C C M } }$ by sweeping through a range of values for the contraction rate λ. Figure 1 plots the normalized (with respect to w¯) RCI bound in (29) as a function of λ.

![](images/9c288246e83cf0a58c619b6b7164cc82b2c7243c3083a5beb1e47e45e4080728.jpg)  
Fig. 1: RCI bound vs contraction rate.

From the plot above, the optimal solution for λ, denoted $\lambda ^ { * }$ , is found to be 1.74 with an associated normalized RCI bound value of 0.7333. In particular, we found that the optimal dual metric is in fact constant for all $x \in \mathcal { X } \mathrm { ~ ( i . e . }$ , is a flat metric), and is given by:

$$
W (x) = \left[ \begin{array}{c c} 2. 0 9 5 0 8 & - 0. 4 6 0 6 6 \\ - 0. 4 6 0 6 6 & 1. 8 5 8 3 3 \end{array} \right].
$$

Therefore, in this scenario, since the geodesic between two points is simply given by the straight line connecting those two points, we use the geodesic distance bound derived in

Theorem V.1 instead of the Euclidean bound, yielding the following RCI set:

$$
\Omega = \{x \in \mathcal {X}: x ^ {T} W ^ {- 1} x \leq 0. 2 1 9 3 \bar {w} ^ {2} \}.
$$

In Figure 2 we plot the above RCI set using $\bar { w } = 0 . 1$ , along with the RCI set computed in [14] for the same disturbance level (in [14], a linear state feedback ancillary controller is used). As one can see, our approach yields a markedly smaller invariant set for a given disturbance upper bound.

![](images/8d85d210ef1b457bd4bf80cbae63898f63164b9d2ed9dc0d1cdb07c5bef6d833.jpg)  
Fig. 2: Comparison of the CCM-derived RCI set (shaded black) and the RCI set computed in [14] (shaded red) for the same disturbance upper bound $\bar { w } = 0 . 1$

## C. Performance of Differential Controllers

In this section we compare the ρ−multiplier and optimized differential controllers. In particular, consider an unconstrained scenario where we ignore the state and control constraints and attempt to regulate the state to the origin. Figure 3 compares the resulting control trajectories when using the ρ−multiplier and optimized differential controllers for regulating the state from the point (3, 3) to the origin.

Notice that there is a large initial spike in control effort when using the ρ−multiplier differential controller. This is due to the fact that the contraction condition in (24) is a sufficient condition for guaranteeing convergence at rate at least $\lambda ^ { * }$ . In particular, consider Figure 5 which plots the geodesic energy $\mathcal { E } ( \gamma )$ over time. One can see that the ρ−multiplier differential controller provides convergence with a rate faster than $\lambda ^ { * }$ . On the other hand, the optimized differential controller (aimed at minimizing control effort) provides a control input equal to zero at points along the geodesic which are naturally contracting at rate $\lambda ^ { * }$ and consequently, the geodesic energy curve for the corresponding state trajectory overlaps nearly exactly with the upper bound dictated by $\lambda ^ { * }$ . The reduction in the maximum control effort is quite drastic, as observed in Figure 3. The corresponding state trajectories are shown in Figure 4.

## D. Comparison with State-of-the-Art-Methods

Finally, in this section we compare the optimal differential controller with its state-of-the-art counterparts. Specifically, we implemented the tube MPC algorithms from [17] and [14] for system (41). In order to ensure fair comparison, we kept the nominal MPC algorithm parameters the same for all three implementations, namely, MPC recalculation sample time $\delta \mathrm { ~  ~ { ~ \sigma ~ } ~ } = \mathrm { ~  ~ { ~ 0 . 1 ~ \mathrm ~ { ~ s ~ } ~ } ~ }$ , MPC time horizon $T ~ = ~ 1 . 5 ~ \mathrm { ~ s ~ }$ , and control trajectory time resolution $\varepsilon _ { t } ~ = ~ 0 . 0 1 ~ { \mathrm { ~ s ~ } } \left( { \mathrm { i . e . } } \right.$ the ancillary feedback was computed every 0.01 s). For the tube algorithm in [17], which uses a secondary MPC controller for computing the ancillary feedback, we varied the recalculation time resolution while maintaining the same control trajectory resolution of 0.01 s. The terminal control invariant set $\ddot { \chi _ { f } }$ was also taken from [14] and kept fixed. The initial state was $( 3 . 5 , - 2 . 5 )$

![](images/9021ac79376ba6a70237d430378736e074d72aaa96dde18f13fcee75f55a439c.jpg)  
Fig. 3: Control trajectories using the ρ−multiplier and optimized differential controllers for system (41) in the unconstrained case starting at (3, 3).

![](images/8c836582688d0b59e85734a4c07d2c3d154696301d49e01984876aa3691b5feb.jpg)  
Fig. 4: Closed-loop state trajectories using the ρ−multiplier (top) and optimized differential (bottom) controllers for system (41) in the unconstrained case starting at (3, 3).

Consider the state trajectory plot in Figure 6, obtained assuming a constant disturbance with magnitude 0.1. Due to space constraints, we present this plot only for the optimal differential controller. The actual trajectory (in blue) is virtually indistinguishable from that predicted by the nominal MPC algorithm. Under the optimized differential ancillary controller the state has a steady-state value equal to (0.02575, 0.006689), while the steady-state values using the algorithms from [14] and [17] were found to be (0.03891, 0.01437) and (0.072, 0.034), respectively. The control histories are almost identical and satisfy the control bounds (Figure 7 shows the control trajectory using the contraction-based method).

While the performance characteristics in terms of stateboundedness and control effort for all three algorithms are in the same order of magnitude for this example, the “dual-$\mathbf { M P C } ^ { \mathrm { , } \mathrm { , } }$ algorithm from [17] is less suitable for fast, continuous dynamical systems as it essentially requires solving an additional optimal control problem for the ancillary controller (albeit with relaxed constraints) – a heavier computational burden. Indeed, relaxing the nominal and/or ancillary re-computation times for this controller led to noticeably worse performance. In addition, as mentioned earlier and in [17], the bound on the deviation of the state from the nominal MPC trajectory is difficult to quantify exactly. On the other hand, the algorithm from [14] is perhaps most computationally efficient since the ancillary controller has an analytical closed-loop form and requires no iterations. However, through efficient parameterization of the problem (as discussed in Section VI), the additional computational burden of computing the optimized differential controller is negligible (consistently in the order of 3-4 milliseconds). Furthermore, our method of optimizing the RCI set by analyzing the intrinsic properties of the non-linearities in the system dynamics rather than relying on Lipschitz bounds yielded a significant reduction in the size of the RCI set. This has important consequences for increasing the domain of feasibility for the nominal MPC problem which uses tightened constraints. In the appendix, we provide an additional example where the resulting metric is not flat, i.e., it is a smooth function of the state, and characterize the robust stabilizability and performance characteristics using our generalized, optimized tube-MPC framework.

![](images/c777ba5cd08b6e1c7078309554d16400ff1b8afb35ac864b496811804e439103.jpg)  
Fig. 5: Comparison of the geodesic energy $\mathcal { E } ( \gamma )$ for the state trajectories corresponding to the ρ−multiplier and optimized differential controllers, respectively.

## VIII. CONCLUSIONS

In this paper we presented a contraction-based approach to tube MPC. Specifically, we presented (a) methods to derive bounds on the size of the invariant tubes as a function of the properties of the control contraction metric and the associated ancillary feedback controller, (b) a tractable quasiconvex optimization program to compute a control contraction metric that minimizes the size of these tubes, and (c) an optimization program (together with numerical methods for its solution) to design a differential controller that optimizes control effort. Future work should address (1) extensions for model uncertainties, (2) offset-free tracking via integral feedback, and (3) robustness to time delays, together with experiments on physical systems.

## REFERENCES

[1] J. Qin and T. Badgwell, “A survey of industrial model predictive control technology,” Control Engineering Practice, vol. 11, no. 7, pp. 733–764, 2003.

[2] Y. Wang and S. Boyd, “Fast model predictive control using online optimization,” IEEE Transactions on Control Systems Technology, vol. 18, no. 2, pp. 267–278, 2010.

[3] D. Q. Mayne, “Model predictive control: Recent developments and future promise,” Automatica, vol. 50, no. 12, pp. 2967–2986, 2014.

[4] D. Mayne, J. Rawlings, C. Rao, and P. Scokaert, “Constrained Model Predictive Control: Stability and Optimality,” Automatica, vol. 36, no. 6, pp. 789–814, 2000.

![](images/d42ccab2fd4aab266dcd985809fb57718518aa1644cb05bf022bdd6e3ceeaa3f.jpg)  
(a) Full closed-loop state trajectory.

![](images/f154299400075c96186aea7ab4dd890338ce7d233558d228246ac678262a2135.jpg)  
(b) Zoomed-in view, which highlights the RCI sets centered on the nominal trajectory.

Fig. 6: Closed-loop state trajectory using the optimized differential ancillary controller. Red: nominal MPC solution, Blue: actual trajectory. Also plotted is the terminal invariant set (shaded red) and the RCI sets along the nominal MPC trajectory (shaded black).

[5] D. Limon, T. Alamo, D. Raimondo, D. M. de la Pena, J. Bravo, A. Ferramosca, and E. Camacho, “Input-to-state stability: a unifying framework for robust model predictive control,” in Nonlinear model predictive control. Springer, 2009, pp. 1–26.

[6] B. Kouvaritakis and M. Cannon, “Developments in Robust and Stochastic Predictive Control in the Presence of Uncertainty,” ASCE-ASME Journal of Risk and Uncertainty in Engineering Systems, Part B: Mechanical Engineering, vol. 1, no. 2, p. 021003, 2015.

[7] L. Magni, D. M. Raimondo, and F. Allgower, ¨ Nonlinear model predictive control. Springer, 2009.

[8] D. M. Raimondo, D. Limon, M. Lazar, L. Magni, and E. F. Camacho, “Min-max model predictive control of nonlinear systems: A unifying overview on stability,” European Journal of Control, vol. 15, no. 1, pp. 5–21, 2009.

[9] D. Mayne, “Robust and stochastic mpc: Are we going in the right direction?” IFAC-PapersOnLine, vol. 48, no. 23, pp. 1–8, 2015.

[10] D. Limon, I. Alvarado, T. Alamo, and E. Camacho, “Robust tubebased MPC for tracking of constrained linear systems with additive disturbances,” Journal of Process Control, vol. 20, no. 3, pp. 248–260, 2010.

[11] W. Langson, I. Chryssochoos, S. Rakovic, and D. Q. Mayne, “Robust ´ model predictive control using tubes,” Automatica, vol. 40, no. 1, pp. 125–133, 2004.

[12] D. Mayne, M. Seron, and S. Rakovic, “Robust model predictive control´ of constrained linear systems with bounded disturbances,” Automatica, vol. 41, no. 2, pp. 219–224, 2005.

[13] M. Rubagotti, D. M. Raimondo, A. Ferrara, and L. Magni, “Robust model predictive control with integral sliding mode in continuous-time

![](images/df08185db97eb59938c2d3b7e2ffdd77ef4f0630766e33115dc25f41e242432f.jpg)  
Fig. 7: Control trajectory using the optimized differential controller for computing the ancillary feedback. Blue: nominal MPC solution, Red: net control applied.

in Advanced Strategies in Control Systems with Input and Output Constraints. Springer, 2007, pp. 207–235.

sampled-data nonlinear systems,” IEEE Transactions on Automatic Control, vol. 56, no. 3, pp. 556–570, 2011.

[14] S. Yu, C. Maier, H. Chen, and F. Allgower, “Tube MPC scheme based¨ on robust control invariant set with application to Lipschitz nonlinear systems,” System and Control Letters, vol. 62, no. 2, pp. 194–200, 2013.

[15] S. Yu, C. Bohm, H. Chen, and F. Allgower, “Robust model predictive control with disturbance invariant sets,” in American Control Conference, 2010, pp. 6262–6267.

[16] S. Rakovic, A. Teel, D. Mayne, and A. Astolfi, “Simple robust control ´ invariant tubes for some classes of nonlinear discrete time systems,” in Decision and Control, 2006 45th IEEE Conference on. IEEE, 2006, pp. 6397–6402.

[17] D. Q. Mayne, E. C. Kerrigan, E. Van Wyk, and P. Falugi, “Tube-based robust nonlinear model predictive control,” International Journal of Robust and Nonlinear Control, vol. 21, no. 11, pp. 1341–1353, 2011.

[18] F. Bayer, M. Burger, and F. Allg ¨ ower, “Discrete-time incremental ¨ iss: A framework for robust nmpc,” in Proc. European Control Conf.(ECC), 2013, pp. 2068–2073.

[19] F. A. Fontes, L. Magni, and E. Gyurkovics, “Sampled-data model<sup>´</sup> predictive control for nonlinear time-varying systems: Stability and robustness,” in Assessment and Future Directions of Nonlinear Model Predictive Control. Springer, 2007, pp. 115–129.

[20] J. Rawlings and D. Mayne, Model predictive control: Theory and design. Nob Hill Publishing, 2013.

[21] I. R. Manchester and J.-J. E. Slotine, “Control contraction metrics: Convex and intrinsic criteria for nonlinear feedback design,” arXiv preprint arXiv:1503.03144, 2015.

[22] F. Uhlig, “A recurring theorem about pairs of quadratic forms and extensions: A survey,” Linear algebra and its applications, vol. 25, pp. 219–237, 1979.

[23] W. M. Boothby, An introduction to differentiable manifolds and Riemannian geometry. Gulf Professional Publishing, 2003, vol. 120.

[24] D. E. Kirk, Optimal control theory: an introduction. Courier Corporation, 2012.

[25] H. Khalil and J. Grizzle, Nonlinear systems. Prentice hall Upper Saddle River, 2002, vol. 3.

[26] Z. Lu and T. K. Pong, “Minimizing condition number via convex programming,” SIAM Journal on Matrix Analysis and Applications, vol. 32, no. 4, pp. 1193–1211, 2011.

[27] P. A. Parrilo, “Semidefinite programming relaxations for semialgebraic problems,” Mathematical programming, vol. 96, no. 2, pp. 293–320, 2003.

[28] K. Leung and I. R. Manchester, “Chebyshev pseudospectral method for nonlinear stabilization using control contraction metrics,” in American Control Conference (ACC), 2016, in Press.

[29] A. V. Rao, “A survey of numerical methods for optimal control,” Advances in the Astronautical Sciences, vol. 135, no. 1, pp. 497–528, 2009.

[30] R. Findeisen, T. Raff, and F. Allgower, “Sampled-data nonlinear¨ model predictive control for constrained continuous time systems,”

## APPENDIX

## IX. NON-POLYNOMIAL DYNAMICS

## A. Dynamics Model & Constraints

We now investigate the performance of contraction-based tube MPC for a non-polynomial, second-order, nonlinear dynamics system (taken from [30]):

$$
\begin{array}{l} \dot {x} (t) = \left[ \begin{array}{c} x _ {2} (t) \\ - x _ {1} (t) + x _ {2} (t) \sinh \left(x _ {1} ^ {2} (t) + x _ {2} ^ {2} (t)\right) \end{array} \right] + \left[ \begin{array}{l} 0 \\ 1 \end{array} \right] u (t) \\ + \left[ \begin{array}{l} 0 \\ 1 \end{array} \right] w (t) \end{array} \tag {42}
$$

The control constraints are:

$$
- 1 \leq u (t) \leq 1 \quad \text {   for   all   } t \geq 0.
$$

The state constraints, while not explicitly given in [30], are taken to be the $[ - 1 . 3 , 1 . 3 ] ^ { 2 }$ box. These bounds were selected based on two factors. The first is an attempt to increase the problem feasibility domain from that achievable using a feedback linearizing controller or an infinite-horizon optimal controller. Both are shown in [30] to be unable to stabilize the closed-loop system when subject to control saturation. The second factor stems from the approximation used to derive a suitable CCM using a polynomial expansion for the dynamics and the dual metric. This is discussed further in the following subsection. The disturbance $w ( t )$ is norm bounded, namely $\lVert w \rVert \leq 0 . 0 2$ , and the state and control cost matrices are $Q = \mathrm { d i a g } ( 0 , 1 )$ and $R = 1$

## B. CCM Synthesis and RCI set

As the dynamics in (42) are non-polynomial, we used a Taylor series approximation for the hyperbolic terms in $f ( x )$ and $\partial f ( x ) / \partial { \bar { x } }$ within problem ${ \mathcal { O P T } } _ { \widehat { C C M } }$ . Consequently, the size of the state constraint set $\mathcal { X }$ was adjusted appropriately to ensure that the desired pointwise LMIs hold for the original non-polynomial dynamics.

As for the example in the main body of the paper, we solved problem $\mathcal { O } \bar { \mathcal { P } } \mathcal { T } _ { \widehat { C C M } }$ by sweeping through a range of values for the contraction rate λ. Figure $\check { 8 }$ plots the normalized (with respect to w¯) RCI bound in (29) as a function of λ.

![](images/c72292331ca30d8d0c46acef788e0c625e72ada6adcfa56961446e6e36e8abb8.jpg)  
Fig. 8: RCI bound vs contraction rate. The maximum allowed degree for the polynomials in $W$ was 4 and the maximum allowed condition number was 50.

From the plot above, $\lambda ^ { * }$ was chosen to be 2 with an associated normalized RCI bound value of 2.31. Note that while feasible solutions were found up to $\lambda ~ = ~ 3$ , these solutions yielded more aggressive controllers that required lower sampling times when implemented using zero-orderhold. The optimal dual metric was

$$
W (x) = \left[ \begin{array}{c c} 0. 2 5 4 3 & - 0. 5 5 8 6 \\ - 0. 5 5 8 6 & 2. 4 9 2 4 - 0. 0 0 6 5 x _ {1} ^ {4} \end{array} \right].
$$

As the optimal CCM for this example is a function of $x ,$ we must use the conservative Euclidean bound to define the RCI set, given below:

$$
\Omega = \{e \in \mathbb {R} ^ {n}: \| e \| \leq 2. 3 1 \bar {w} \},
$$

where we recall from Definition III.1 that $e = x { - x ^ { * } }$ . Having obtained our RCI set, we are now ready to implement the contraction based tube MPC algorithm.

## C. Integration with Tube MPC

The terminal set $\chi _ { f }$ for the nominal MPC problem was taken from [30] as a sublevel set of the terminal cost function:

$$
\begin{array}{c} \Psi (x) = \exp \big (\| x \| ^ {2} \big) - 1, \\ \mathcal {X} _ {f} = \{x \in \mathcal {X}: \Psi (x) \leq 0. 5 3 \}. \end{array}
$$

Note that this is simply the set $\{ x \in \mathcal { X } : \| x \| ^ { 2 } \leq 0 . 4 3 \}$ . The algorithm timing characteristics were $\delta \stackrel {  } { = } 0 . 1 \ s , \ T \stackrel { } { = } 2 \ s ,$ and $\varepsilon _ { t } ~ = ~ 0 . 0 0 5 ~ \mathrm { s } .$ The initial state for the nominal MPC problem at solve time $t _ { i }$ was allowed to be an optimization variable as discussed in Remark III.4 subject to the constraint given in (14) using a tightened RCI set. This was done to avoid transient overshoots due to the zero-order-hold implementation. Figures 9 and 10 show the resulting state and control trajectories respectively for an initial state given by $( - 1 , - 0 . 2 )$ and assuming a constant disturbance signal $\dot { w } ( t ) ~ = ~ \bar { w } ~ = ~ 0 . 0 2$ . All state and control constraints are satisfied and the true state converges to a steady-state value of (0.002, 0.0001) while remaining inside the RCI set centered on $x ^ { * } ( t )$ at all times.

![](images/d93fd91d197a1ac299802b602ef4696171c7d46815fc067b887e6189a61abd51.jpg)  
(a) Full closed-loop state trajectory.

![](images/d897c08b16938234fb5e86bf7f119f878f939cc98e56671d8e9759be34a50d97.jpg)  
(b) Zoomed-in view, which highlights the RCI sets (shaded green and red) centered on the nominal trajectory.

Fig. 9: Closed-loop state trajectory using the optimized differential ancillary controller. Red: nominal MPC solution, Blue: actual trajectory. Also plotted is the terminal invariant set (shaded grey) and the RCI sets along the nominal MPC trajectory (shaded red and green).

![](images/db595fd3135aa56aa3dc9fb877375dd4fc49f9c6d6dbf9b2ed2137d0eb43c2b5.jpg)  
Fig. 10: Control trajectory using the optimized differential controller for computing the ancillary feedback. Blue: nominal MPC solution, Red: net control applied.