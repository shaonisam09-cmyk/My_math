# Sliding-window theory exploration — 2026-09-08

This file records candidate results derived from the supplied TeX manuscript, without modifying the manuscript. The results concern the abstract binary-event controller. They do **not** establish corresponding properties of the multiplicative relaxation update coupled to the full MPC solver. Independent audit is required before incorporation.

## 1. Definitions and the key simplification

Suppress the constraint index. Let the state at time t be the oldest-to-newest binary word

\[
 X_t=(O_{t-w+1},\ldots,O_t)\in\{0,1\}^w,
 \qquad K_t=\sum_{j=t-w+1}^t O_j,\qquad Y_t=K_t/w.
\]

The retained count after discarding the oldest bit is

\[
 S_t=K_t-O_{t-w+1}\in\{0,\ldots,w-1\}.
\]

The feedback numerator in the manuscript simplifies exactly to

\[
 b_t=\alpha-Y_t+\frac{2O_{t-w+1}-1}{2w}
     =\frac{w\alpha-S_t-1/2}{w}.
\]

Thus the probability selector suggested by the one-step calculation depends on the **retained count**, rather than separately on the whole-window count and oldest bit. All conditional probabilities below are with respect to a filtration containing the complete binary-event past.

Select an integer k nearest to w alpha. Resolve a possible halfway tie to favor this fixed k. The corresponding endpoint selector is

\[
 q_s=\begin{cases}u,&s<k,\\l,&s\ge k,\end{cases}
 \qquad 0<l<u<1. \tag{1}
\]

If w alpha = n+1/2, choosing k=n means q_n=l, while choosing k=n+1 means q_n=u. Both are legitimate one-step minimizers. For any non-halfway target, k is unique and (1) is precisely the strict-sign selector. In this file alpha denotes the selector's target. When comparing with the manuscript's weak assumption, use alpha=alpha-star if projection changes the target.

## 2. Exact finite-time settling and periodicity in the ideal case

**Proposition A.** Use (1) with l=0 and u=1 and the fixed tie convention above. Starting from any binary word of length w, the count reaches k in a finite number tau of transitions. More precisely:

* If K_0<k, tau is the position of the (k−K_0)-th zero in the initial word, and tau<=k.
* If K_0>k, tau is the position of the (K_0−k)-th one in the initial word, and tau<=w−k.
* If K_0=k, tau=0.

The two nontrivial bounds are sharp whenever the respective initial-count case is possible. After tau, the entire word rotates by one position each step, so O_{t+w}=O_t for all subsequent generated bits. The period divides w, K_t=k forever, and the long-run ordinary time average equals k/w.

**Proof.** If K_t<k, then S_t<=K_t<k, so O_{t+1}=1. The count increases by one exactly when an initial zero leaves. Until the count first reaches k, it never decreases; it cannot overshoot because increments are at most one. The required number of zeros is k−K_0. Its position is at most K_0+(k−K_0)=k, since at most K_0 ones can precede it. This also shows that the relevant outgoing bits are all initial bits, since k<=w. Equality is attained when the initial word consists of all K_0 ones followed by all zeros.

If K_t>k, then S_t>=K_t−1>=k, so O_{t+1}=0. Exactly the same argument, interchanging zero and one, gives tau equal to the position of the (K_0−k)-th one and tau<=w−k; equality is attained by placing all initial zeros before all initial ones.

At K_t=k, if the oldest bit is zero then S_t=k and the new bit is zero; if the oldest bit is one then S_t=k−1 and the new bit is one. Hence the new bit always copies the oldest, proving rotation and periodicity. A period of w has exactly k ones, giving the ordinary time average k/w. QED.

**Halfway tie with arbitrary selection.** If w alpha=n+1/2 and the tie q_n can be chosen arbitrarily in [0,1], replace the target singleton by {n,n+1}. If K_0<n, the first entry occurs within n transitions; if K_0>n+1, it occurs within w−n−1 transitions. The set is invariant. Periodicity is not asserted for arbitrary time-dependent tie decisions. The fixed tie convention recovers Proposition A.

**Meaning.** The manuscript's asymptotic convergence can be sharpened to an exact finite-time statement with a sharp bound, under the same ideal event-selection abstraction. It also reveals a limitation: the ideal solution locks into a periodic binary pattern, which need not distribute violations evenly inside shorter subwindows.

## 3. Exact stationary law for a broad count-response family

**Assumption B.** For deterministic numbers q_0,...,q_{w−1} satisfying 0<q_s<1, the actual conditional probability obeys

\[
 \Pr(O_{t+1}=1\mid\mathcal F_t)=q_{S_t}. \tag{2}
\]

No independent-Bernoulli assumption is made. Equation (2) makes the binary window a time-homogeneous finite-state Markov chain; serial dependence is generally present.

Define

\[
 a_0=1,\qquad a_j=\prod_{s=0}^{j-1}\frac{q_s}{1-q_s},\quad j=1,\ldots,w,
 \qquad Z=\sum_{j=0}^w\binom wj a_j.
\]

**Theorem B (exact invariant distribution).** Under Assumption B, the chain is irreducible and aperiodic and has the unique invariant distribution

\[
 \pi(x)=\frac{a_{|x|}}{Z},\qquad x\in\{0,1\}^w, \tag{3}
\]

where |x| is its number of ones. Consequently its stationary count has mass function

\[
 \mu_j=\Pr_\pi(K=j)=\frac{\binom wj a_j}{Z}, \qquad
 \frac{\mu_{j+1}}{\mu_j}
 =\frac{w-j}{j+1}\frac{q_j}{1-q_j}. \tag{4}
\]

Conditional on K=j, all binary words with j ones are equally likely. This is a property of the invariant law, not a claim of independence over time or of reversibility.

**Proof.** Every prescribed word can be generated in w steps with positive probability, because each appended bit has probability strictly between zero and one. Thus any state reaches every state. The all-zero state has a positive self-loop, giving aperiodicity.

For stationarity, fix a target word z=(z_1,...,z_w), set b=z_w and r=sum_{i=1}^{w−1}z_i. Its only predecessors are (0,z_1,...,z_{w−1}) and (1,z_1,...,z_{w−1}), with counts r and r+1. Both use the same retained count r in the next transition. The incoming probability under (3) is therefore

\[
 \frac{a_r+a_{r+1}}Z q_r^b(1-q_r)^{1-b}.
\]

The recursion a_{r+1}=a_r q_r/(1-q_r) implies that this is a_r/Z when b=0 and a_{r+1}/Z when b=1. In both cases it equals pi(z). The distribution normalizes by counting binomially many words of each count. Irreducibility gives uniqueness; (4) follows by direct division. QED.

For the endpoint policy (1), the weights are particularly simple:

\[
 a_j=\left(\frac u{1-u}\right)^{\min(j,k)}
      \left(\frac l{1-l}\right)^{(j-k)_+}. \tag{5}
\]

Equations (3)–(5) give exact finite-w values for stationary bias, MAE, variance, and any count tail using only w+1 weights instead of 2^w states. For numerical use, evaluate the weights in log space to avoid overflow.

## 4. Geometric concentration and O(1/w) tracking error

**Theorem C (stationary concentration).** Use the endpoint policy (1). For 1<=k<=w−1 define

\[
 r_+=\frac{(w-k)l}{(k+1)(1-l)},\qquad
 r_-=\frac{k(1-u)}{(w-k+1)u}.
\]

Assume r_+<1 and r_-<1. Equivalently,

\[
 l<\frac{k+1}{w+1},\qquad u>\frac{k}{w+1}. \tag{6}
\]

Then for every integer j>=1,

\[
 \Pr_\pi(K\ge k+j)\le r_+^j,\qquad
 \Pr_\pi(K\le k-j)\le r_-^j. \tag{7}
\]

In particular,

\[
 \mathbb E_\pi|Y-\alpha|
 \le \left|\frac kw-\alpha\right|
 +\frac1w\left(\frac{r_+}{1-r_+}+\frac{r_-}{1-r_-}\right), \tag{8}
\]

and

\[
 \mathbb E_\pi(K-k)^2
 \le \frac{r_+(1+r_+)}{(1-r_+)^2}
    +\frac{r_-(1+r_-)}{(1-r_-)^2}. \tag{9}
\]

The boundary cases k=0 and k=w have only one tail; formulas extend by setting the absent-tail ratio to zero, when the remaining ratio is less than one.

**Proof.** For n>=k, (4) gives

\[
 \frac{\mu_{n+1}}{\mu_n}
 =\frac{w-n}{n+1}\frac l{1-l}\le r_+.
\]

Therefore mu_{n+j}<=r_+^j mu_n for n>=k and n+j<=w. Summing over n=k,...,w−j yields

\[
 \sum_{m=k+j}^w\mu_m
 \le r_+^j\sum_{n=k}^{w-j}\mu_n\le r_+^j.
\]

For n<=k,

\[
 \frac{\mu_{n-1}}{\mu_n}
 =\frac n{w-n+1}\frac{1-u}u\le r_-.
\]

The reversed summation proves the lower-tail bound. The tail-sum identity for the nonnegative integer variable |K−k| gives

\[
 \mathbb E_\pi|K-k|
 =\sum_{j\ge1}\Pr_\pi(|K-k|\ge j)
 \le\sum_{j\ge1}(r_+^j+r_-^j).
\]

This yields (8) by the triangle inequality. The corresponding identity for the second moment uses weights 2j−1. Since sum_{j>=1}(2j−1)r^j=r(1+r)/(1−r)^2, (9) follows. QED.

For epsilon>0, (7) also implies

\[
 \Pr_\pi\!\left(|Y-\alpha|\ge\left|k/w-\alpha\right|+\epsilon\right)
 \le r_+^{\lceil w\epsilon\rceil}+r_-^{\lceil w\epsilon\rceil}. \tag{10}
\]

**Fixed strict-interior target.** Fix l<alpha<u independently of w, and choose a nearest k=k_w. Then k_w/w tends to alpha and

\[
 r_+\longrightarrow\frac{l(1-\alpha)}{\alpha(1-l)}<1,
 \qquad r_-\longrightarrow\frac{\alpha(1-u)}{(1-\alpha)u}<1.
\]

Thus (6) holds for sufficiently large w, the constants in (8)–(9) remain bounded, and

\[
 \mathbb E_\pi|Y-\alpha|=O(w^{-1}),\qquad
 \mathbb E_\pi(Y-\alpha)^2=O(w^{-2}). \tag{11}
\]

Moreover fixed-epsilon deviations have probability at most O(exp(−c w epsilon)), with c>0 depending on l,u,alpha. This is stronger than the O(w^{-1/2}) MAE of the manuscript's constructive constant-probability policy, but it has different assumptions: it describes the actual endpoint selector in stationarity, with a strict target margin, rather than any feasible policy or the actual MPC.

The strict-interior qualification matters. If projection puts the target at l or u, one of the limiting ratios equals one; (11) does not follow. Claims covering an unattainable or boundary target require a separate analysis.

## 5. An unavoidable fluctuation bound for every admissible policy

**Proposition D.** Suppose every conditional probability of any considered policy lies in [l,u], and set delta=min(l,1−u)>0. Then for every time at which the retained window is known,

\[
 \mathbb E[|Y_{t+1}-\alpha|\mid\mathcal F_t]\ge\frac\delta w. \tag{12}
\]

No stationarity, Markov, independence, or count-response assumption is needed.

**Proof.** With S=S_t and p=Pr(O_{t+1}=1|F_t), both p and 1−p are at least delta. Therefore

\[
 \begin{aligned}
 w\mathbb E[|Y_{t+1}-\alpha|\mid\mathcal F_t]
 &=(1-p)|S-w\alpha|+p|S+1-w\alpha|\\
 &\ge\delta\bigl(|S-w\alpha|+|S+1-w\alpha|\bigr)\\
 &\ge\delta,
 \end{aligned}
\]

where the final inequality is the triangle inequality for two points separated by one. QED.

Together with the manuscript's stationarity-based projection-gap lower bound and the unavoidable lattice error, a stationary policy restricted to [l,u] satisfies

\[
 \mathbb E|Y-\alpha|\ge
 \max\left\{\operatorname{dist}(\alpha,[l,u]),\frac\delta w,
 \min_{j=0,\ldots,w}|\alpha-j/w|\right\}.
\]

**Rate-optimality corollary.** For a fixed strict-interior alpha and fixed 0<l<u<1, Theorem C and Proposition D imply that the best achievable stationary MAE among policies restricted to [l,u] is Theta(1/w). The endpoint selector attains this rate. This is an order statement, not an assertion that the selector minimizes the exact average cost at each finite w.

The restriction to [l,u] is important: the manuscript calls this an attainable **inner** interval. If the controller can also choose probabilities outside it, Proposition D and this minimax comparison do not constrain those broader policies.

## 6. Counterexample: one-step optimal does not mean average-cost optimal

Take

\[
 w=2,\quad\alpha=13/50,\quad l=1/10,\quad u=1/2.
\]

Here l<alpha<u, the unique nearest count is k=1, and the one-step greedy selector uses q_0=1/2 and q_1=1/10. Formula (5) gives a_0=1, a_1=1, a_2=1/9 and Z=28/9. Hence

\[
 \Pr_\pi(K=0,1,2)=(9,18,1)/28.
\]

Its stationary MAE is

\[
 \frac9{28}\frac{13}{50}
 +\frac{18}{28}\frac{12}{50}
 +\frac1{28}\frac{37}{50}
 =\frac{37}{140}\approx0.264285714.
\]

The feasible constant policy p_t=1/10 makes the bits independent Bernoulli(1/10), so its stationary count masses are (81,18,1)/100. Its MAE is

\[
 \frac{81}{100}\frac{13}{50}
 +\frac{18}{100}\frac{12}{50}
 +\frac1{100}\frac{37}{50}
 =\frac{653}{2500}=0.2612.
\]

The greedy policy costs more by 27/8750>0. This exact counterexample rules out promoting the manuscript's one-step proposition to unrestricted finite-w average-cost optimality without additional conditions or a different policy.

## 7. Explicit finite-time convergence to the invariant regime

Suppose Assumption B holds with l<=q_s<=u for every s. Let

\[
 \eta=(l+1-u)^w,\qquad
 \nu(z)=\frac{l^{|z|}(1-u)^{w-|z|}}{(l+1-u)^w}.
\]

For every starting state x and target word z, generating exactly z over the next w steps has probability at least l^{|z|}(1−u)^{w−|z|}. Thus

\[
 P^w(x,\cdot)\ge\eta\nu(\cdot).
\]

The standard elementary minorization decomposition writes P^w=eta nu+(1−eta)R. It contracts total variation by at most 1−eta. Hence, with total variation defined as sup_A|mu(A)−nu(A)|,

\[
 \|\mathcal L(X_t)-\pi\|_{\mathrm{TV}}
 \le(1-\eta)^{\lfloor t/w\rfloor}. \tag{13}
\]

For any initial state, (8) consequently implies

\[
 \mathbb E|Y_t-\alpha|
 \le\left|k/w-\alpha\right|
 +\frac1w\left(\frac{r_+}{1-r_+}+\frac{r_-}{1-r_-}\right)
 +(1-\eta)^{\lfloor t/w\rfloor}. \tag{14}
\]

Here the expectation comparison uses 0<=|Y−alpha|<=1. The mixing bound is rigorous but may be extremely pessimistic for large w; it should not be used to claim a practical settling time. Finite-state ergodicity also gives almost-sure convergence of time averages of bounded window statistics to their stationary expectations.

## 8. A verified robustness extension, and its boundary

Theorem B remains exact for arbitrary deterministic q_s in (0,1). Suppose, for a fixed threshold k,

\[
 q_s\ge u_{\mathrm{eff}}\quad(s<k),\qquad
 q_s\le l_{\mathrm{eff}}\quad(s\ge k),
 \qquad 0<l_{\mathrm{eff}}<u_{\mathrm{eff}}<1.
\]

The upper-tail ratio in (4) is bounded by the same expression with l_eff replacing l; the lower-tail ratio is bounded by the same expression with u_eff replacing u. Every statement (7)–(11) therefore holds with those replacements whenever the resulting ratios are less than one.

In particular, if a count-dependent response satisfies |q_s−q_s^ideal|<=epsilon, where q^ideal is (1), then take l_eff=l+epsilon and u_eff=u−epsilon. For fixed

\[
 0\le\epsilon<\min\{\alpha-l,u-\alpha\},
\]

the stationary O(1/w) rate survives for sufficiently large w. This provides an explicit probability-response margin.

This result **still assumes the actual probability is a time-homogeneous function of the retained count alone**. A dependence on hidden MPC states, earlier history, or time destroys the proved exchangeable invariant-law calculation. The present derivation does not establish the same rate under those more general dependencies. It is preferable to state this limitation directly instead of treating a small response error as a sufficient general closed-loop theorem.

## 9. Fixed-window pathwise convergence is impossible under nondegenerate noise

**Proposition E.** For any adapted policy whose conditional probabilities always lie in a fixed interval 0<l<=p_t<=u<1, and any fixed finite w,

\[
 \liminf_{t\to\infty}Y_t=0,\qquad
 \limsup_{t\to\infty}Y_t=1\quad\text{almost surely}. \tag{15}
\]

This statement does not require a Markov policy or stationarity.

**Proof.** Partition the generated bits into consecutive, nonoverlapping blocks of length w. Conditional on the history before any block, the probability that the entire next block consists of ones is at least l^w, by repeatedly conditioning on each additional bit. Consequently, the conditional probability that none of the next N blocks is all ones is at most (1−l^w)^N; this follows inductively by conditioning at block boundaries. Letting N tend to infinity shows that the probability of never seeing another all-one block after any fixed block boundary is zero. Taking the countable union over boundaries proves that all-one blocks occur infinitely often almost surely. Likewise, all-zero blocks have conditional probability at least (1−u)^w and occur infinitely often almost surely. At the corresponding block ends the sliding-window average equals one or zero, proving (15). QED.

Thus stationary concentration and small stationary expected error cannot be interpreted as almost-sure convergence of a fixed-window average to an interior target. Rare extremes remain inevitable over an infinite horizon. Proposition D also rules out convergence in probability to alpha for fixed w: bounded convergence in probability would imply convergence of the expected absolute error to zero, contradicting (12).

## 10. Suggested manuscript use

The strongest internally connected package is: exact stationary law (Theorem B), geometric concentration and O(1/w) stationary tracking (Theorem C), and the universal delta/w fluctuation floor (Proposition D). Together they explain why active window feedback can improve the order of accuracy over independent Bernoulli sampling. Proposition A is a concise strengthening of the existing ideal theorem. The finite-time mixing bound is technically useful but pessimistic. The average-cost counterexample is a guardrail against overclaiming.

Before incorporation, an independent reviewer should verify the filtration, the interpretation of the interval as inner versus exhaustive, endpoint/tie conventions, and the distinction between abstract probability selection and the actual multiplicative h update.
