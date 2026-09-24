This article was downloaded by: [149.171.67.164] On: 21 August 2015, At: 19:07 Publisher: Institute for Operations Research and the Management Sciences (INFORMS) INFORMS is located in Maryland, USA

## Management Science

## MANAGEMENT SCIENCE

![](manne1960_lp_sequential_decisions_assets/images/71b61d37a43a7df286e9aefe11a03b8d676de90d28f82b6b20e52ce41220d915.jpg)

Publication details, including instructions for authors and subscription information: http://pubsonline.informs.org

## Linear Programming and Sequential Decisions

Alan S. Manne,

To cite this article:

Alan S. Manne, (1960) Linear Programming and Sequential Decisions. Management Science 6(3):259-267. http:// dx.doi.org/10.1287/mnsc.6.3.259

## Full terms and conditions of use: http://pubsonline.informs.org/page/terms-and-conditions

This article may be used only for the purposes of research, teaching, and/or private study. Commercial use or systematic downloading (by robots or other automatic processes) is prohibited without explicit Publisher approval, unless otherwise noted. For more information, contact permissions@informs.org.

The Publisher does not warrant or guarantee the article’s accuracy, completeness, merchantability, fitness for a particular purpose, or non-infringement. Descriptions of, or references to, products or publications, or inclusion of an advertisement in this article, neither constitutes nor implies a guarantee, endorsement, or support of claims made of that product, publication, or service.

© 1960 INFORMS

Please scroll down for article—it is on subsequent pages

## informs

INFORMS is the largest professional society in the world for professionals in the fields of operations research, management science, and analytics.

For more information on INFORMS, its publications, membership, or meetings visit http://www.informs.org

# LINEAR PROGRAMMING AND SEQUENTIAL DECISIONS\*†

ALAN S. MANNE\*\*

Cowles Foundation, Yale University

Using an illustration drawn from the area of inventory control, this paper demonstrates how a typical sequential probabilistic model may be formulated in terms of (a) an initial decision rule and (b) a Markov process, and then optimized by means of linear programming. This linear programming technique may turn out to be an efficient alternative to the functional equation approach in the numerical analysis of such problems. Regardless of computational significance. however, it is of interest that there should be such a close relationship between the two traditionally distinct areas of dynamic programming and linear programming

## 1. Summary

Using an illustration drawn from the area of inventory control, this paper demonstrates how a typical sequential probabilistic model may be formulated in terms of (a) a decision rule, specifying order quantities as a function of initial stock levels, and (b) a Markov process in which the transition probabilities depend both upon the decision rule and also upon the probability distribution of demands. Optimization of the decision rule is accomplished by means of linear programming.

In contrast with the linear programming studies of Dantzig [4] and Radner [10], the time horizon considered here is infinite rather than finite. For a study very closely related to this one, the reader is referred to a paper written by R. Howard [7].

The essential idea underlying this linear programming formulation is that the "state" variable i (initial stock level) and the "decision" variable j (order $j$ quantity) are introduced as subscripts to the unknowns xij. These unknowns $\pmb { x _ { i j } }$ $\pmb { x _ { i j } }$ represent the joint probabilities with which the state variable takes on the value of i and the decision variable the value of $j .$ With an infinite time horizon, it is then possible to derive equilibrium distributions (steady state probabilities) of inventory levels. production quantities, and shortage levels. The requirements of statistical equilibrium furnish the linear restraints, and the objective function to be minimized consists of the expected cost level corresponding to the equilibrium probabilities.

Although the particular application described is a rather specialized one, there seem to be quite a number of dynamic programming problems in which this computational technique may prove to be an efficient alternative to the usual iterative method for solving functional equations. As yet, there is only a limited amount of evidence available for comparing the effectiveness of the two approaches from the viewpoint of numerical analysis. Regardless of computational significance, however, it is of interest that there should be such a close relationship between the two traditionally distinct areas of dynamic programming and linear programming.

## 2. Formulation of the Problem

This is a single-item inventory problem in which the initial stock on hand at the beginning of each "month" is, in Bellman's terminology, the "state variable." [2, p. 81] The size of initial inventory will be indicated by the subscript i. The quantity produced within the month is the "decision variable," and the amount produced will be indicated by the subscript $j .$ Our problem is to obtain an optimal sequential decision rule—that is, to specify a value of $j$ for each value taken on by i.

The sum of initial inventory plus the quantity produced will be known as the "available stock," and its size will be denoted by k.

The quantity demanded during the month is a serially independent random variable, n. The symbol ${ \pmb p } _ { \pmb n }$ represents the probability with which n units will be demanded.

The size of month-end terminal inventories will be indicated by t. If backlogs of demand are to be ruled out, t = max $( 0 , k - n )$ 1

Once that a decision rule and a demand probability distribution have been specified, the inventory process may be regarded as a Markov chain. From this chain may be calculated the equilibrium probability distribution of inventory levels, of production quantities, and of shortage levels. It will be assumed that the decision rule is to be specified in such a way as to minimize the expected monthly costs corresponding to these equilibrium probabilities. (Note that this objective is closely related to, but by no means identical with that of minimizing discounted expected costs.)

The relevant costs here consist of the sum of the expected value of three components: (1) those costs related to the initial inventory levels i, (2) those related to the production quantities $j ,$ and (3) those related to the shortage levels $( n - k )$ . Symbolically, total costs are expressed as follows:

$$
\varepsilon C _ {1} (i) + \varepsilon C _ {2} (j) + \varepsilon C _ {3} (n - k)\tag{1}
$$

No convexity restrictions are imposed upon any of the three functions $\tilde { C } _ { 1 } ( i )$ $\mathcal { C } _ { 2 } ( j )$ , and $C _ { 3 } ( n - k ) .$ 1 Convexity is, in effect, brought about by supposing that mixed strategies are available. In other words, the conditional probability of taking action $j$ (given that the initial inventory is at level i) may lie anywhere in the closed interval between zero and unity."

Some fairly light restrictions are imposed upon the quantities $i , j , k , n ,$ and t. First, they must be non-negative integers. Second, there must exist a positive integer ${ \pmb T } ,$ , an upper limit upon inventory accumulation, such that:

$$
t = \max (0, k - n) \leq T
$$

The linear programming problem described below will involve $T + 1$ equations. In order for the simplex computations to be carried out with presentday electronic machine programs, it would be necessary to choose units in such a way that the integer $\pmb { T }$ does not exceed something of the order of 200.

## 3. Some Definitions

DF: $y _ { i } ~ = ~ \mathrm { p r o b a b i l i t y }$ that a month's initial stock equals $i \cdot ( \sum _ { i } y _ { i } = 1 . )$ DF: $\pmb { y } _ { t } ^ { \prime }$ = probability that a month's terminal stock equals $t \cdot ( \sum \ j ^ { \prime } , \ j ^ { \prime } , \ j = 1 . )$ Statistical equilibrium requires:

$$
y _ {t} = y _ {t} ^ {\prime}\tag{2}
$$

$$
(t = 0, 1, \dots , T)
$$

DF: $\pmb { x _ { i j } } =$ joint probability with which the initial stock equals i and the production quantity equals $j .$ 1

$$
\therefore \sum_ {j} x _ {i j} = y _ {i}\tag{3}
$$

$$
(i = 0, 1, \dots , T)
$$

and

$$
\sum_ {i, j} x _ {i j} = 1\tag{4}
$$

DF: $z _ { k } =$ probability that the available stock equals k

$$
\therefore z _ {k} = \sum_ {\substack {i, j: \\ i + j = k}} x _ {i j}\tag{5}
$$

$$
(k = 0, 1, \dots , T)
$$

DF: $p _ { \mathfrak { n } } = \mathbf { p r o b a b i l i t y }$ that n units are demanded within the month.

N.B. The probabilities ${ \pmb p } _ { \pmb { \mathscr { n } } }$ are independent of any choices made by the decision-maker. The probabilities $x _ { i j } , \ y _ { i } , \ y ^ { \prime } _ { t }$ , and ${ \pmb z } _ { { \pmb k } }$ , however are directly under his control. (Note that once the joint probabilities $\pmb { x } _ { i j }$ have been specified, it is straightforward to reconstruct the decision rule—i.e., the conditional probability of taking action ${ \bf { \nabla } } j ,$ given the initial stock level i.)

## 4. Relationships between the Individual Probabilities

Since the random variable n is independent of the available stock $\pmb { k } ,$ and since $t = \operatorname* { m a x } \left( 0 , k - n \right)$ •

$$
\begin{array}{l} y _ {0} ^ {\prime} = \sum_ {\substack {k, n: \\ k - n \leq 0}} p _ {n} z _ {k} \\ y _ {t} ^ {\prime} = \sum_ {\substack {k, n: \\ k - n = t}} p _ {n} z _ {k} \end{array}\tag{6}
$$

$$
(t = 1, 2, \dots , T)
$$

By (5):

$$
y _ {0} ^ {\prime} = \sum_ {\substack {i, j, n: \\ i + j - n \leq 0}} p _ {n} x _ {i j}\tag{7}
$$

$$
y _ {t} ^ {\prime} = \sum_ {\substack {i, j, n: \\ i + j - n = t}} p _ {n} x _ {i j} \quad (t = 1, 2, \dots , T)
$$

By (2) and (3), we finally arrive at the interdependence relationships between the individual unknowns $\pmb { x _ { i j } }$

(8.0)

$$
\sum_ {j} x _ {0 j} = \sum_ {\substack {i, j, n: \\ i + j - n \leq 0}} p _ {n} x _ {i j}\tag{8.t}
$$

$$
\sum_ {j} x _ {t j} = \sum_ {\substack {i, j, n: \\ i + j - n = t}} p _ {n} x _ {i j} \quad (t = 1, 2, \dots , T)
$$

Equations (8.0) - (8.T) may each be interpreted as a requirement of statistical equilibrium. In each of these equations, the left-hand side measures the probability with which the initial monthly inventory level will be $\mathbf { \delta t } ,$ and the right-hand side the probability with which the terminal level will equal t. Statistical equilibrium implies that these two probabilities must coincide.

The unknowns in the linear programming model are the joint probabilities $\pmb { x _ { i j } }$ . The constraints consist of the usual non-negativity conditions upon the xij , together with equations (4) and (8.1)−(8.T). Equation (8.0) is redundant, and need not be included explicitly within the constraint set.

## 5. Expected Costs

The cost coefficient associated with each of the $\pmb { x _ { i j } }$ will be known as $\pmb { c _ { i j } }$ The total cost expression to be minimized by means of the simplex procedure is as follows:

$$
\sum_ {i, j} c _ {i j} x _ {i j}\tag{9}
$$

How do we assign values to the coefficients $\pmb { c _ { i j } }$ so as to be consistent with the minimand given previously by expression (1)? Note that:

$$
\varepsilon C _ {1} (i) = \sum_ {i} y _ {i} C _ {1} (i) = \sum_ {i, j} x _ {i j} C _ {1} (i)
$$

$$
\varepsilon C _ {2} (j) = \sum_ {i, j} x _ {i j} C _ {2} (j)
$$

$$
\varepsilon C _ {3} (n - k) = \sum_ {i, j} x _ {i j} \sum_ {n} p _ {n} C _ {3} (n - i - j)
$$

The cost coefficient $\pmb { c } _ { \pmb { i } \pmb { j } }$ associated with the unknown $\pmb { \mathscr { x } } _ { i j }$ is therefore constructed as follows;

$$
c _ {i j} = C _ {1} (i) + C _ {2} (j) + \sum_ {n} p _ {n} C _ {3} (n - i - j)\tag{10}
$$

## 6. A Numerical Example

In order to construct a numerical example, it is necessary to assign values to the demand probabilities, to the three cost functions, and to the upper limit placed upon inventory accumulation. For illustrative purposes, we will work with the following:

$$
\begin{array}{l l} p _ {0} = \frac {2}{3} & C _ {1} (i) = i \quad T = 3 \\ p _ {1} = 0 & C _ {2} (j) = 3 j \\ p _ {2} = \frac {1}{3} & C _ {3} (n - i - j) = \max [ 0, 6 (n - i - j) ] \end{array}
$$

In addition, it will be assumed that the production capacity is at most one unit per month $( \ i . { \ e } , \ j =$ either 0 or 1). Note that the mean demand level amounts to only  of this capacity limit. There is, however, a  probability that demand will actually amount to twice the production limit.

Table 1 contains a calculation of the cost coefficients for this problem, and Table 2 indicates the constraint matrix in detached coefficients form. In transcribing equations (8.1)-(8.3) into this matrix, the right-hand side shown earlier in the text has been subtracted from the left-hand side. Equation (8.1), for example, has been transformed as follows:

$$
\sum x _ {1 j} - \sum_ {\substack {i, j, n: \\ i + j - n = 1}} p _ {n} x _ {i j} = 0
$$

TABLE 1  
Calculation of the cost coefficients $\pmb { c _ { i , \eta } }$

<table><tr><td>Identification subscripts (i, j) ....</td><td>(0,0)</td><td>(0,1)</td><td>(1,0)</td><td>(1,1)</td><td>(2,0)</td><td>(2,1)</td><td>(3,0)</td></tr><tr><td>Inventory costs =  $C_1(i) = i$ </td><td>0</td><td>0</td><td>1</td><td>1</td><td>2</td><td>2</td><td>3</td></tr><tr><td>Production costs =  $C_2(j) = 3j$ </td><td>0</td><td>3</td><td>0</td><td>3</td><td>0</td><td>3</td><td>0</td></tr><tr><td>Shortage costs = $\Sigma_n p_n C_3(n - i - j) =$  $\Sigma_n p_n \max [0, 6(n - i - j)]$ </td><td>4</td><td>2</td><td>2</td><td>0</td><td>0</td><td>0</td><td>0</td></tr><tr><td>Total cost coefficient =  $c_{ij}$ </td><td>4</td><td>5</td><td>3</td><td>4</td><td>2</td><td>5</td><td>3</td></tr></table>

TABLE 2

Detached coefficients matrix

<table><tr><td>Identification subscripts (i, j)......</td><td>(0,0)</td><td>(0,1)</td><td>(1,0)</td><td>(1,1)</td><td>(2,0)</td><td>(2,1)</td><td>(3,0)</td><td>Constant terms</td></tr><tr><td>Equation (4)</td><td>1</td><td>1</td><td>1</td><td>1</td><td>1</td><td>1</td><td>1</td><td>= 1</td></tr><tr><td>Equation (8.1)</td><td>0</td><td> $-\frac{2}{3}$ </td><td> $\frac{1}{3}$ √</td><td>1√</td><td>0</td><td> $-\frac{1}{3}$ </td><td> $-\frac{1}{3}$ </td><td>= 0</td></tr><tr><td>Equation (8.2)</td><td>0</td><td>0</td><td>0</td><td> $-\frac{2}{3}$ </td><td> $\frac{1}{3}$ </td><td>1</td><td>0</td><td>= 0</td></tr><tr><td>Equation (8.3)</td><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td><td> $-\frac{2}{3}$ </td><td> $\frac{1}{3}$ </td><td>= 0</td></tr><tr><td>Optimal activity levels,  $x_{ij}$ </td><td>—</td><td> $\frac{1}{3}$ </td><td>—</td><td> $\frac{2}{5}$ </td><td> $\frac{4}{5}$ </td><td>—</td><td> $\varepsilon^{*}$ </td><td></td></tr><tr><td>Conditional probability of or-dering quantity j—given an inventory level of i. $\frac{x_{ij}}{\sum x_{ij}}$ </td><td>0</td><td>1</td><td>0</td><td>1</td><td>1</td><td>0</td><td>1*</td><td></td></tr></table>

\* To eliminate the question of degeneracy, it is convenient to regard the value of $\pmb { x _ { 3 } . 0 }$ as $\bullet ,$ a "small" positive quantity.

Also shown in Table 2 is the optimal linear programming solution to the problem. According to this calculation, the initial inventory will be at a zero level during $\scriptstyle { \frac { 1 } { 3 } }$ of the months, at a unit level $\frac { 2 } { 5 }$ of the time, and at a level of two during the remaining $\frac { 4 } { 8 } . 3$ The conditional probabilities derived from this solution indicate the following decision rule: Whenever the initial inventory has dropped to a level of either zero or unity, one unit of production is ordered. At higher initial levels, no production takes place at all. Note that no mixed strategies are indicated despite the fact that this option was built into the model.

## 7. Some Observations

(1) There are a number of paths by which one may prove that it will always be optimal to adopt pure strategies. One way is sketched out in the accompanying note by Harvey Wagner. Another-and perhaps a more intuitive way—is to follow the line of reasoning by which Dvoretzky, Kiefer, and Wolfowitz dismiss mixed strategies in a problem of this sort. This is a problem in which the demand probabilities ${ \pmb p } _ { \pmb { n } }$ are known in advance to the decision-maker, and do not have to be estimated by him. [5, p. 191 n.] Hence the conclusion that in a two-person game in which the decision-maker has "found $\mathrm { o u t } ^ { \prime \prime }$ his opponent's strategy, it will never hurt him to restrict his own choice of strategies to pure ones.

(2) The choice of an upper limit, T, upon inventory accumulation is admittedly an arbitrary one. $\mathbf { I f } ,$ after finding an optimal solution for a given value of ${ \boldsymbol { \mathit { \mathbf { T } } } } ,$ and observing that $x _ { \tau - j , j } = 0$ for all $j ,$ it is entirely possible that a further increase in the value of $\pmb { T }$ will lower the minimand still further. It is a simple matter to construct pathological cost functions that will yield this result. Lest the reader become too concerned over this potential snare, it is worth pointing out that there are a number of applications in which there exist very real upper limits upon the accumulation of inventory, e.g., the reservoir capacity of a hydroelectric system.

(3) It is not altogether legitimate to have brushed aside the question of initial conditions for the Markov process. If the optimal matrix in the linear programming solution is a "decomposable" one, the initial conditions will clearly govern the ultimate statistical equilibrium. The most direct way to circumvent this difficulty would be to assume that the initial conditions lie within the control of the decision-maker-at least to the extent that he may choose them so as to start off within any one of the subsystems into which the larger system splits up.

(4) It is possible to attach an economic interpretation to the implicit prices (dual variables) associated with the linear programming solution. They represent the amount by which total costs would be altered if the initial inventory were at the t th level rather than at zero.º Apparently, they are related to the solution of Bellman's functional equation for the inventory problem. [2, pp. 159-164] This being so, it should be a comparatively simple matter to use them in order to link together a non-stationary finite-horizon model with a stationary one having an infinite horizon.

## 8. Areas of Application

Among the applications that suggest themselves, the following stochastic models would seem to be of the most interest:

(1) Changes in the rate of production. A number of studies have been concerned with systems in which the costs depend not only upon the rate of production (as in the example above), but also upon the rate of change of that level. (E.g., [6].) This kind of problem could be attacked through the same methods outlined here by defining the "state variable" i as a pair of numbers: one representing the initial inventory level and the other the rate of production during the immediately preceding period. With this one change in interpretation, things would proceed in essentially the same way that has been suggested here. The only serious difficulty might arise from the computational costs involved in an increase in the number of equations within the linear programming model. Instead of just one equation for each of the $( T + 1 )$ levels of inventory, there would now be r equations—one for each of the r discrete rates of production that were considered. Altogether, the programming matrix would contain $r \cdot ( T + 1 )$ rows.

(2) Seasonal storage of inventories. Several recent papers have been focussed upon the problem of optimization under conditions of seasonally fluctuating demands (e.g., the demand for heating oil [3]) or of supplies (e.g., the supply of water for hydroelectric installations [9]). In order for a linear programming model to reflect such seasonal fluctuations in the probability distribution of demands or of supplies, the state variable i would again have to represent a pair of numbers—the first indicating the season of the year and the second the inventory level at the beginning of the particular season. The conditions of statistical equilibrium would then imply equality between probabilities for the terminal inventories of one season and the initial inventories of the one following. With s seasons and $( T + 1 )$ inventory levels in each, a total of $s ( T + 1 )$ equations would be involved. Even with time subdivided into 12 individual months and with 10 levels of inventory considered during each month, the computational requirements would still remain modest—a 120-equation system.

(3) Multi-location inventory problems. In the event that inventories are scattered among several geographical locations, it may no longer be appropriate to describe the system in terms of a single state variable—the aggregate quantity held in stock. Instead, a separate quantity must be specified for each location.⁶ $\mathbf { I f } ,$ then, there are stocks held at l different locations, the state variable i will have to be regarded as an l-tuplet of numbers. With $( T + 1 )$ alternative inventory levels at each individual location, the linear programming model would contain no less than $( T + 1 ) ^ { l }$ distinct equations. As far as any realistic problems are concerned, it must be conceded that this number of equations could become hopelessly large. Even with just four locations and five inventory levels at each, the system would contain 625 equations! The most obvious way to reduce the size of such problems would be to devise some judicious scheme for aggregation into a manageable number of geographical areas.

(4) Delivery lags. Each of the cases described thus far has been based upon the assumption that delivery lags are short-that any production ordered at the beginning of a period will be available to satisfy whatever demand takes place within the period. With long delivery lags, these models hardly seem to be appropriate.

A number of authors [1, 8] have shown, however, that there is a simple way to analyze a problem in which there are long but fixed delivery lagsthat is no randomness in the time required for delivery. (This formulation guarantees that all currently outstanding orders will have been received prior to the arrival of any order placed currently.) In addition to non-random delivery lags, these authors also assume that a shortage in supply is reflected in a temporary backlog rather than in a permanent loss of demand.

With these assumptions, the appropriate state variable required in order to describe the system is no longer the actual inventory on hand, but rather the sum of that inventory plus all outstanding orders. To adapt this suggestion to the linear programming model discussed here, all that needs to be done is to reinterpret the state variable i as "stock on hand plus orders outstanding." The probability ${ \pmb p } _ { \pmb { \mathscr { n } } }$ would be regarded as the probability that n units were demanded during whatever time interval is required for the delivery of an order. This variant upon the inventory model is equally well adapted to the case in which time is regarded as a discrete or as a continuous parameter.

## 9. An Unresolved Difficulty

The minimand employed here represents the average level of costs per unit of time, and completely ignores the dating of these costs. Time discounting is neglected—just as in many other treatments of the inventory problem. The only justification for this procedure must be that the mean interval between successive recurrences of any given inventory levelthat this mean interval is short relative to the discount factor.

In cases involving equipment analysis, however, this simplification seems quite unpalatable. The interval between successive replacements of a piece of equipment is likely to be measured in years rather than months [11]. With such models, the "present worth" form of minimand appears essential. It will be of considerable interest to see whether the current linear programming formulation of Markov processes can be extended to the case of time discounting.

## References

1. BeckmANN, M., ANd R. MutH, "Inventory Policy for a Case of Lagged Delivery," Management Science, January 1956.

2. BELLmAN, R., Dynamic Programming, Princeton, 1957.

3. CHarNEs, A., W. W. CoopEr, ANd G. H. Symonds, "An Approach to Stochastic Programming of Heating Oil,"Management Science, April 1958.

4. DANTziG, G. B., "Linear Programming under Uncertainty," Management Science, April-July, 1955.

5. DvoRETzKY, A., J. KiEFER, AND J. WoLrowItz, "The Inventory Problem: I. Case of Known Distributions of Demand,"Econometrica, April 1952.

6. HoLT, C., F. ModIGLIaNI, AND H. SImoN, "A Linear Decision Rule for Production and Employment Scheduling,"Management Science, October 1955.

7. Howarp, R., "Discrete Dynamic Programming and Sequential Investment Problems," presented to the Econometric Society on August 26, 1958, Cambridge, Mass.

8. KARLIN, S., AND H. Scar, "Inventory Models with Time Lag," Ch. 10 of Studies in the Mathematical Theory of Inventory and Production, Stanford, 1958.

9. LirrLE, J. D. C., "The Use of Storage Water in a Hydroelectric System," Journal of the Operations Research Society of America, May 1955.

10. RadnE, R., "The Application of Linear Programming to Team Decision Problems," Management Science, January 1959.

11. TEBoGí, G., Dynamic Equipment Replacement Policy, McGraw-Hill Book Co., New York, 1949.

12. WAGNER, HaRvEy M., "On the Optimality of Pure Strategies," Management Science, April 1960.

Copyright 1960, by INFORMS, all rights reserved. Copyright of Management Science is the property of INFORMS: Institute for Operations Research and its content may not be copied or emailed to multiple sites or posted to a listserv without the copyright holder's express written permission. However, users may print, download, or email articles for individual use.