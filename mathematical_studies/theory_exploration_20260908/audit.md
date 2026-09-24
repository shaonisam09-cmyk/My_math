# Independent proof audit — 2026-09-08

Scope: adversarial verification of extensions to the FIFO window feedback method in `IEEE-conference-template-062824.tex`. Per the user's subsequent clarification, the theoretical object is the manuscript formula; existing MATLAB variants are excluded from the conclusions. Manuscript and implementation were not edited.

## 1. Verified extension: exact stationary distribution of the probability-policy surrogate

**Verdict: correct**, provided the complete policy is specified as a function of the retained count and its probabilities are strictly between zero and one. This is a theorem for the abstract probability controller, not yet for the implemented multiplicative relaxation controller.

Let the window state be $X_t=(O_{t-w+1},\ldots,O_t)\in\{0,1\}^w$, let $K_t=|X_t|$, and put $S_t=K_t-O_{t-w+1}$. The policy selects $O_{t+1}\mid X_t\sim\mathrm{Bern}(q_{S_t})$, where fixed numbers $q_s\in(0,1)$ are specified for every $s=0,\ldots,w-1$. Conditional here means conditional on the full observed past as well; the transition probabilities must genuinely depend only on $S_t$.

Define

$$a_0=1,\qquad a_{k+1}=a_k\frac{q_k}{1-q_k},\quad 0\le k<w,\qquad Z=\sum_{k=0}^{w}\binom wk a_k.$$

Then the unique invariant law is

$$\pi(x)=\frac{a_{|x|}}Z,\qquad \Pr_\pi(K=k)=\frac{\binom wk a_k}Z.$$

**Independent proof.** Fix a target word $y=(v,b)$, where $v$ is a binary word of length $w-1$, $s=|v|$, and $b\in\{0,1\}$. Its only predecessors are $(0,v)$ and $(1,v)$, which have counts $s$ and $s+1$. Consequently the unnormalized incoming stationary mass is

$$ (a_s+a_{s+1})q_s^b(1-q_s)^{1-b}.$$

For $b=0$ the ratio defining $a_{s+1}$ makes this expression equal to $a_s$; for $b=1$ it equals $a_{s+1}$. These are exactly the desired weights $a_{|y|}$. Normalizing proves invariance. Every target word can be produced from every source word in exactly $w$ transitions, with positive probability. Thus the chain is irreducible; the all-zero state has self-loop probability $1-q_0>0$, so it is aperiodic. A finite irreducible aperiodic chain has a unique invariant law and converges to it from every initial state. No assumption that $K_t$ alone is Markov is needed or generally true.

For completeness, if $\epsilon=\min_s\min(q_s,1-q_s)>0$, then $P^w(x,y)\ge\epsilon^w$ for all words $x,y$. This gives a Doeblin mass $(2\epsilon)^w$ and a valid, although often very pessimistic, total-variation contraction bound over blocks of length $w$.

### 1.1 Verified stronger stationary error bound

Assume $w\alpha$ is not a half-integer, set $k_*=\operatorname{round}(w\alpha)$, and choose $q_s=u$ for $s<k_*$ and $q_s=l$ for $s\ge k_*$, where $0<l<u<1$. This is precisely the one-step optimal endpoint policy for the unprojected target $\alpha$ because the decision statistic is $\alpha-(s+1/2)/w$.

Define

$$r_+=\frac{(w-k_*)l}{(k_*+1)(1-l)},\qquad r_-=\frac{k_*(1-u)}{(w-k_*+1)u}.$$

If $r_+<1$ and $r_-<1$, then, for each integer $j\ge1$,

$$\Pr_\pi(|K-k_*|\ge j)\le r_+^j+r_-^j,$$

and hence

$$\mathbb E_\pi|K/w-\alpha|\le |k_*/w-\alpha|+\frac1w\left(\frac{r_+}{1-r_+}+\frac{r_-}{1-r_-}\right).$$

**Independent proof.** Writing $p_k=\Pr_\pi(K=k)$ gives

$$\frac{p_{k+1}}{p_k}=\frac{w-k}{k+1}\frac{q_k}{1-q_k}.$$

For $k\ge k_*$ the right-hand side is at most $r_+$; for $k\le k_*$, $p_{k-1}/p_k\le r_-$. Iterating yields $p_{k_*+j+m}\le r_+^j p_{k_*+m}$ for all indices in range. Summing over $m\ge0$ yields $\Pr(K\ge k_*+j)\le r_+^j\Pr(K\ge k_*)\le r_+^j$. The lower-tail proof is identical with descending indices. The two tails are disjoint for $j\ge1$. Summing tail probabilities uses the identity $\mathbb E|K-k_*|=\sum_{j\ge1}\Pr(|K-k_*|\ge j)$ and proves the claimed error bound after applying the triangle inequality.

For fixed $l<\alpha<u$, these ratios converge as $w\to\infty$ to

$$\frac{(1-\alpha)l}{\alpha(1-l)}<1,\qquad \frac{\alpha(1-u)}{(1-\alpha)u}<1.$$

They are therefore bounded below one for all sufficiently large $w$, and the stationary error is $O(1/w)$. Strict interior reachability is essential for this uniform statement. It is not enough that $\alpha\in(0,1)$ or that $l\le\alpha\le u$.

**Tie and boundary audit.** If $w\alpha=k+1/2$, every probability is one-step optimal when $S_t=k$. Choosing $q_k=l$ gives center $k$ and the formulas above; choosing $q_k=u$ gives center $k+1$. An arbitrary tie value requires using its actual adjacent ratio. At $k_*=0$ or $k_*=w$, one tail is empty, with its displayed ratio equal to zero; the remaining ratio still must be below one to use a geometric series. Endpoints $l=0$ or $u=1$ are outside the positive-probability invariant-law theorem and require a separate communicating-class analysis.

## 2. Exact obstruction: the compensation changes the feedback target

In the paper's multiplicative update, let $b_t=\alpha-Y_t+(2O_{t-w+1}-1)/(2w)$. Substitution of the window sum gives the exact identity

$$b_t=\alpha-\frac1{2w}-\frac1w\sum_{j=t-w+2}^{t}O_j.$$

Thus the outgoing observation cancels entirely. When $w=1$, the statistic is the constant $\alpha-1/2$ and has no feedback information at all. For $w>1$, its zero-mean target is

$$\alpha_w=\frac{w\alpha-1/2}{w-1}=\alpha+\frac{\alpha-1/2}{w-1}.$$

This is harmless for the intended oracle comparison but must be included in any theorem about the actual repeated multiplicative update.

**Smooth monotone response counterexample.** Take $w=1$, $\alpha\in(0,1/2)$, $r_t=-h_t>0$, and the smooth strictly increasing attainable response $p_{t+1}=r_t/(1+r_t)$. The target $p=\alpha$ is attainable at $r=\alpha/(1-\alpha)$. Nevertheless, for $\chi>1/2-\alpha$,

$$r_t=r_0\left(1+\frac{\alpha-1/2}{\chi}\right)^t\longrightarrow0,$$

so $p_{t+1}\to0$, rather than $\alpha$. At $\alpha=1/2$, $r_t$ is constant and the achieved probability is entirely determined by initialization. Hence a smooth monotone relaxation-to-probability relationship alone cannot prove the proposed update converges to its nominal target.

More generally, if $\alpha<1/(2w)$, every possible $b_t$ is bounded above by the negative constant $\alpha-1/(2w)$; therefore the unclipped multiplicative magnitude tends geometrically to zero. If $\alpha>1-1/(2w)$, every possible $b_t$ is bounded below by a positive constant, and the magnitude tends geometrically to infinity.

## 3. Fixed-gain log drift imposes a further bias

For the unprojected update $r_t=r_{t-1}(1+b_t/\chi)$ with positive factors, put $z_t=\log r_t$. If the joint process is stationary and $z_t$ is integrable, then

$$0=\mathbb E(z_t-z_{t-1})=\mathbb E\log(1+b_t/\chi).$$

Since $\log(1+x)\le x$, with strict inequality unless $x=0$, this implies $\mathbb E b_t\ge0$, strictly positive whenever $\Pr(b_t\ne0)>0$. If $\mu=\mathbb E O_t$ and $w>1$, then

$$\mu\le\alpha_w,$$

with strict inequality for nondegenerate feedback. In particular, exact stationary mean tracking $\mu=\alpha$ is impossible for $\alpha<1/2$ under these assumptions, and also for $\alpha=1/2$ unless $b_t=0$ almost surely.

The same conclusion follows without integrable stationary $z_t$ if one separately establishes the appropriate vanishing time-average log increment and ergodic averages. Neither stationarity nor that boundary behavior is automatic: it must be proved for the response model used.

## 4. Why the original $O(w^{-1/2})$ result is not a universal controller bound

The paper's constant conditional-probability construction is valid when the conditioning filtration contains all prior violation indicators. Constant conditional probability then implies independence of the Bernoulli indicators by iterated conditioning. Its concentration inequality applies after the window consists entirely of observations generated by that policy.

The construction proves existence of a policy with a given performance, not that every attainable-interval policy or the implemented $h$ update has that performance. Even stationarity, ergodicity, and an interval bounded away from zero and one do not imply the same universal constant.

**Explicit stationary ergodic counterexample.** Let $O_t$ be a stationary two-state Markov chain with flip probability $a=10^{-4}$, so $\Pr(O_{t+1}=1\mid O_t=0)=a$ and $\Pr(O_{t+1}=1\mid O_t=1)=1-a$. Its stationary marginal mean is $\alpha=1/2$, all conditional probabilities lie in the fixed attainable interval $[a,1-a]$, and the chain is irreducible and aperiodic. In a window of length $w=100$, the no-flip event has probability $(1-a)^{99}$ and yields $|Y_t-1/2|=1/2$. Thus

$$\mathbb E|Y_t-1/2|\ge\frac12(1-10^{-4})^{99}>0.495,$$

whereas $1/(2\sqrt{100})=0.05$. The displayed numerical lower bound follows from Bernoulli's inequality $(1-a)^{99}\ge1-99a=0.9901$. This counterexample does not contradict the constant-policy theorem; it contradicts transferring that theorem to an unspecified controller.

## 5. Verified lower order bound and failure of exact greedy optimality

### 5.1 Universal one-step noise floor within the restricted probability class

For a policy whose actual conditional probabilities satisfy $p\in[l,u]\subset(0,1)$, set $\delta=\min(l,1-u)>0$. For every retained count $S$ and real target $\alpha$,

$$\mathbb E\bigl[|Y_{t+1}-\alpha|\mid\mathcal F_t\bigr]=\frac{(1-p)|S-w\alpha|+p|S+1-w\alpha|}{w}\ge\frac\delta w.$$

**Proof.** Both coefficients are at least $\delta$, while the triangle inequality gives $|S-w\alpha|+|S+1-w\alpha|\ge1$. The assertion is conditional and requires no stationarity. Thus, for fixed $l<\alpha<u$, the geometric-tail upper bound in Section 1 and this universal lower bound prove the best achievable stationary error in the restricted probability class has order $\Theta(1/w)$. This is an order-optimality result and does not claim that the greedy controller attains the exact minimum.

The assumed interval in the paper is an inner attainable interval. A lower bound applying to controllers restricted to it must not be restated as a lower bound over all physically attainable controllers unless an outer probability restriction is separately established.

### 5.2 Exact counterexample to global stationary optimality of the greedy policy

Take $w=2$, $\alpha=13/50$, $l=1/10$, $u=1/2$. The greedy policy has $k_*=1$, $q_0=1/2$, $q_1=1/10$. The invariant law proved above gives

$$\Pr(K=0,1,2)=(9,18,1)/28,$$

and therefore

$$\mathbb E_{\mathrm{greedy}}|K/2-13/50|=\frac{9(13/50)+18(12/50)+37/50}{28}=\frac{37}{140}.$$

The admissible constant-probability policy $p=l=1/10$ has binomial count law $(81,18,1)/100$ and error

$$\mathbb E_{p=l}|K/2-13/50|=\frac{81(13/50)+18(12/50)+37/50}{100}=\frac{653}{2500}<\frac{37}{140}.$$

Thus one-step conditional optimality does not imply global optimality of stationary mean absolute error. The counterexample remains within a strictly interior target $l<\alpha<u$ and strictly positive probabilities.

### 5.3 Almost-sure finite-window convergence is impossible under persistent noise

If the full-past conditional probabilities satisfy $p_t\in[l,u]\subset(0,1)$ at every time, then for every fixed finite window length $w$,

$$\liminf_{t\to\infty}Y_t=0,\qquad \limsup_{t\to\infty}Y_t=1\quad\text{almost surely}.$$

**Proof.** Partition future observations into disjoint blocks of length $w$. By repeated conditioning, the conditional probability that a given next block is all zero, given all prior blocks, is at least $c_0=(1-u)^w>0$. Therefore the conditional probability of no all-zero block in the next $N$ blocks is at most $(1-c_0)^N$, which tends to zero. Starting at each of the countably many block indices shows that all-zero blocks occur infinitely often almost surely. Repeating the argument with all-one probability at least $c_1=l^w>0$ gives infinitely many all-one blocks. At the endpoint of such blocks the moving average is respectively zero or one. Because every moving average belongs to $[0,1]$, the liminf and limsup equal those endpoints.

Consequently an $O(1/w)$ stationary mean-error bound and a stationary geometric tail are compatible with infinitely many rare extreme windows. Neither implies eventual pathwise tracking within a fixed small error band over an infinite operating horizon. This statement requires actual probabilities restricted to a nondegenerate interval; merely possessing an inner attainable interval is insufficient.

### 5.4 Independent finite calculations

As a supplemental consistency check, the full $2^w$-state transition balance was enumerated for $w=1,\ldots,8$ using nonconstant strictly interior $q_s$. The largest absolute residual between the proposed invariant law and one transition of that law was $1.12\times10^{-16}$ in double precision. Exact rational arithmetic independently gave greedy error $37/140$, constant-policy error $653/2500$, and their positive difference $27/8750$. These checks supplement the proofs; they are not substitutes for them.

## 6. Additional local audit observations

* The lower bound based on $[\underline p,\overline p]$ is valid for policies restricted to that interval. Since the paper assumes that interval is an inner subset of the full attainable set, it is not a lower bound for every physically admissible controller.
* Projection need not preserve feedback sign. For example, $w=10$, retained count $S=2$, $\alpha=0.1$, and attainable interval $[0.4,0.6]$ give $b(\alpha)=0.1-0.25<0$ but $b(\alpha^*)=0.4-0.25>0$. The actual update and projected-target greedy policy choose opposite directions.
* Mere stationarity of exogenous disturbances does not justify replacing the full conditional future distribution by an unconditional one-step empirical law in a scalar-state Bellman equation. Independence, a sufficient exogenous Markov state, or an explicitly declared surrogate-model interpretation is required.
* The zero-probability scenario rows in the offline LP need not have uniquely determined $s_{ml}$ at the optimum. The value vector can still be unique, but the assertion that every auxiliary variable equals its inner minimum needs $\rho_l>0$ or a chosen representative optimum.
* The successor-state conversion must use the piecewise charge/discharge energy map when the feasible power interval lies entirely below zero or entirely above zero. In addition to the known grid-spacing and origin omission, using a charge factor for a negative upper power bound is not the actual state transition.

## 7. Fresh audit of the manuscript-update exploration

The independent file `actual_update.md` was read in full after its author supplied candidate proofs. The mathematical substance of Theorems 2–7 passes this audit. The original displayed equation (8) omitted a plus sign before its finite-history boundary term; this was reported to the author for correction. That transcription issue does not affect the proof's correct derivation. A prose sentence called a four-term decomposition a three-term decomposition and was also reported.

### 7.1 Finite-time identity and risk certificate

The exact boundary discrepancy is $D_N=\sum_{j=1}^{n-1}(\sum_{t=1}^{N}O_{t-j}-\sum_{t=1}^{N}O_t)$, so $|D_N|\le\sum_{j=1}^{n-1}j=n(n-1)/2$. The bound also holds when $N<j$ because the difference is at most $N\le j$. Substituting $\log(1+u)=u-r(u)$ and telescoping yields the claimed identity, with the correct negative signs on the log-amplitude growth and logarithmic-curvature remainder. The conditional martingale difference $p_t-O_t$ lies in a history-dependent interval of length one. Conditional Hoeffding's lemma still gives $\exp(\lambda^2/8)$ as its moment-generating-function bound, so the fixed-time average-risk certificate requires no independence of the overlapping windows. Its corrected right-hand side contains **plus** $(w-2)/(2N)$.

### 7.2 Sharp stationary envelope

For $n=w-1$, $f_k=\log(1+(A-k/w)/\chi)$, and $H(z)=w[A-\chi(e^z-1)]$, one has $H(f_k)=k$ and $H''(z)<0$. Therefore the endpoint chord lies below $H$ and bounds the count expectation from below at mean log increment zero. The chord between the two neighboring $f_k$ values straddling zero has an extension lying above $H$ on every allowed discrete support point. It therefore bounds that same expectation from above. Strict concavity gives a strict upper gap below $H(0)$ whenever zero is between two allowed values. The formulas in Theorem 5 follow exactly from these chord intercepts.

The statement carefully distinguishes sharpness over all count distributions satisfying the one log-balance equation from realizability by a stationary process with overlapping windows. This qualification is necessary and correct. Exhaustive enumeration of all feasible one-point and two-point supports, which are the extreme points of this two-equality finite probability polytope, agreed with both claimed bounds across 116 cases with $w=2,\ldots,30$; the maximum double-precision error was $2.23\times10^{-16}$.

### 7.3 Existence of an invariant law

Theorem 6 uses a continuous scalar response $g:\mathbb R\to(0,1)$ with limits zero and one at the respective tails. This is a substantive additional response model, not a consequence of the general multivariate MPC. Under that assumption its proof is complete:

1. A block of all new ones needs exactly $n-1$ arbitrary initial updates before the retained count equals $n$, and thereafter every increment is the same strictly negative $f_n$. The analogous all-zero block gives strictly positive increments $f_0$.
2. Taking a sufficiently long fixed block overcomes those initial arbitrary increments. Bounded log increments ensure every generating amplitude in a block remains in the appropriate tail when its initial amplitude is sufficiently far into that tail.
3. Conditional probability of the appropriate constant-bit block is at least $1-m\epsilon$. Independence is not required. The resulting expected block log increment has a fixed inward sign and magnitude.
4. Expanding $x_{t+m}^2-x_t^2$ produces a drift upper bound $-d|x_t|+C$. Summation on the block skeleton bounds the first moments of its Cesàro occupation measures and makes them tight.
5. Continuous branch probabilities and continuous log-coordinate branch maps imply the Feller property. The weak limit of a Cesàro subsequence is invariant for the block transition; averaging its images over one full block yields an invariant measure for the one-step transition. Lower semicontinuity and bounded increments preserve a finite first log moment.
6. Because every finite bit string has positive conditional probability from every finite state, any invariant law gives positive probability to both extreme retained counts. The log-curvature bias is consequently strict.

There is no unsupported appeal to irreducibility on the unbounded real-valued state space, and no unwarranted claim of uniqueness or convergence from all initial states. The interior target interval is necessary for two-sided feedback in this scalar model, as separately established by its one-directional-increment contradiction.

### 7.4 Additional strengthening discovered during audit: no first moment is needed for stationary bias

The integrability condition on the log amplitude can be removed from the stationary calibration identity and stationary discrete envelope, while preserving the finite positive factor assumptions that make the increments bounded. Let $X$ and $Y$ have the same real-valued marginal distribution and suppose $|Y-X|\le L$. For $T_M(x)=\max(-M,\min(x,M))$,

$$\mathbb E[T_M(Y)-T_M(X)]=0,\qquad |T_M(Y)-T_M(X)|\le|Y-X|\le L.$$

Dominated convergence gives $\mathbb E(Y-X)=0$, even if $\mathbb E|X|=\infty$. Applying this to two consecutive stationary log amplitudes gives $\mathbb E f(S_t)=0$ for every invariant probability law, which is all the stationary bias and chord-envelope proofs require. This strengthening was sent to the author; the invariant-existence theorem's finite first-moment conclusion remains independently useful.

Even the stationary almost-sure calibration statement can avoid the first-moment condition. Birkhoff applied to the bounded stationary increments implies $(x_N-x_0)/N$ has an almost-sure limit. Stationarity of the real-valued $x_N$ gives $x_N/N\to0$ in probability, because $\Pr(|x_N|>\epsilon N)=\Pr(|x_0|>\epsilon N)\to0$. Since also $x_0/N\to0$ almost surely, the Birkhoff limit must be zero. The finite-time calibration identity then gives the same invariant-sigma-field average-risk conclusion.

### 7.5 Audited strengthening: exponential moments and calibration from arbitrary initialization

The author proposed strengthening the block drift argument from Section 7.3; this strengthening also passes independent audit. Retain its constants $m,M,R,d$ such that $|\Delta x|\le M$ over a block and, whenever $|x|\ge R+M$,

$$\mathbb E[\operatorname{sgn}(x)\Delta x\mid Z_t]\le-d/2.$$

No sign crossing is possible over that block because $R>0$ and $|\Delta x|\le M$. Set $D=\operatorname{sgn}(x)\Delta x$, so $|D|\le M$. Taylor's formula gives

$$\mathbb E e^{\theta D}\le1-\theta d/2+\theta^2M^2e^{\theta M}/2.$$

Choose $\theta>0$ so that $\theta M^2e^{\theta M}\le d/2$ and $\theta d/4<1$. With $W(x)=e^{\theta|x|}$ and $\lambda=1-\theta d/4\in(0,1)$, this yields $P^mW\le\lambda W$ outside the compact interval. Inside it, the endpoint satisfies $|x_{t+m}|\le R+2M$, so $P^mW\le K=e^{\theta(R+2M)}$. Hence globally

$$P^mW\le\lambda W+K.$$

Iteration bounds all block-time exponential moments by $W(x_0)+K/(1-\lambda)$. At intermediate times the bounded increments increase $W$ by at most $e^{\theta M}$, proving

$$\sup_{t\ge0}\mathbb E_{Z_0}e^{\theta|x_t|}<\infty$$

for every finite deterministic initial state. Markov's inequality now gives a summable exponential bound for $\Pr(|x_t|>\epsilon t)$; Borel–Cantelli proves $x_t/t\to0$ almost surely. Thus the original finite-time identity yields long-run empirical rate at most $a$ almost surely **from arbitrary finite deterministic initialization**, without a stationary initial distribution or an ergodicity assumption. A bounded martingale-difference strong law gives the identical bound for the time-average conditional violation probability.

### 7.6 Audit-derived strengthening: the whole sharp envelope holds pathwise

The chord argument is pointwise in the count, so it can be averaged along any binary trajectory before imposing stationarity. Write $\Delta x_N=x_N-x_0$. Its lower chord gives, for every $N$,

$$\bar O_N\ge\mu_{\mathrm{lo}}-\frac{\Delta x_N}{N(f_0-f_n)}-\frac{D_N}{nN}.$$

For noninteger $s=w\alpha-1/2$ and $j=\lfloor s\rfloor$, the neighboring upper chord gives

$$\bar O_N\le\mu_{\mathrm{hi}}-\frac{\Delta x_N}{nN(f_j-f_{j+1})}-\frac{D_N}{nN}.$$

For integer $s$, the tangent to $H$ at zero gives instead

$$\bar O_N\le a-\frac{\chi\Delta x_N}{cN}-\frac{D_N}{nN}.$$

These follow directly from $\sum f(S_t)=\Delta x_N$ and $\sum S_t=nN\bar O_N+D_N$; no probabilistic model is needed. Under the response assumption and the exponential stability conclusion just verified, $\Delta x_N/N\to0$ almost surely, while $D_N/N\to0$ for fixed $w$. Consequently

$$\mu_{\mathrm{lo}}\le\liminf_N\bar O_N\le\limsup_N\bar O_N\le\mu_{\mathrm{hi}}\quad\text{almost surely}.$$

The same holds for time-average conditional probabilities. When $w=2$, the two envelope endpoints coincide, so the empirical and conditional-risk time averages converge almost surely to the exact logarithmic formula in the exploration's equation (15), from any finite deterministic initial state. This conclusion does not claim that the fixed-length moving window converges, nor that the response map can be inferred from the full MPC model without further evidence. This extension was sent to the author for inclusion.

### 7.7 Audited quantitative convergence for the actual two-observation window

The subsequently proposed quantitative corollary for $w=2$ also passes audit. Here $n=1$, $S_t=O_t$, and $D_N=0$. With $J=f_0-f_1>0$ and $\mu_2=f_0/J$, telescoping gives exactly

$$\bar O_N-\mu_2=-\frac{x_N-x_0}{JN}.$$

If the established exponential moment bound is $\sup_N\mathbb E e^{\theta|x_N|}\le C_0$, Jensen's inequality yields $\mathbb E|x_N|\le\log(C_0)/\theta$, and hence

$$\mathbb E|\bar O_N-\mu_2|\le\frac{|x_0|+\log(C_0)/\theta}{JN}.$$

The triangle inequality and exponential Markov inequality give, for $u>0$,

$$\Pr(|\bar O_N-\mu_2|>u)\le\min\{1,C_0e^{\theta|x_0|-\theta JNu}\}.$$

For any $b>1/\theta$, the probabilities $\Pr(|x_N|>b\log N)$ are summable, so Borel–Cantelli gives $|\bar O_N-\mu_2|=O(\log N/N)$ almost surely. These are empirical-frequency rates around the exact generally biased $\mu_2$; they do not concern error around the nominal target $\alpha$. Nor do they transfer automatically to the average conditional risk $\bar p_N$, whose difference from $\bar O_N$ contains a martingale average with its own fluctuation scale. The author's qualification of that distinction is necessary and correct.

## 8. Final audit: full-history tail response extension

The final requested extension removes the scalar Markov response assumption for the nonstationary moment and average-risk guarantees. **Verdict: passes**, with the qualifications stated below.

Retain the original update, $n=w-1$, its finite increments $f(k)$, the interior target and positive-factor assumptions. Define

$$\Psi(p)=\mathbb E f(\operatorname{Bin}(n,p)).$$

It is continuous and strictly decreasing, with $\Psi(0)=f(0)>0>f(n)=\Psi(1)$. Thus it has a unique zero $p_\dagger\in(0,1)$. In fact $p_\dagger<a$, because the binomial count at parameter $a$ makes the pre-log feedback mean zero but nonconstant; strict concavity of the logarithm gives $\Psi(a)<0$.

Assume that for some fixed finite $R>0$ and fixed thresholds $0\le p_-<p_\dagger<p_+\le1$, the actual full-history conditional probabilities satisfy, almost surely at every time,

$$x_t\le-R\implies p_{t+1}\le p_-,\qquad x_t\ge R\implies p_{t+1}\ge p_+.$$

The interior behavior may depend arbitrarily on the full observed history. Time homogeneity, a scalar sufficient state, continuity, and a Markov property are not needed for the following moment argument.

Put $u=\Psi(p_-)>0$, $v=-\Psi(p_+)>0$, and $L=\max_k|f(k)|$. Select $m\ge n$ so that

$$d=\min\{(m-n+1)u-(n-1)L,(m-n+1)v-(n-1)L\}>0,$$

and set $M=mL$. Starting at $x_t\ge R+M$ keeps every generating amplitude in the next block above $R$, on every possible observation trajectory. Each fresh group of $n$ bits consequently stochastically dominates a binomial count with parameter $p_+$. Since $f$ is decreasing, every fully refreshed count has conditional expected increment at most $\Psi(p_+)=-v$. The first $n-1$ increments are at most $L$, so the whole block has conditional expected increment at most $-d$. The lower-tail argument is symmetric and gives at least $d$.

An elementary proof of the stochastic comparison avoids any innovation-representation assumption. For an increasing terminal function $\varphi$ and a sequence of $n$ bits with every conditional success probability at least $p_+$, define

$$H_j(k)=\mathbb E\varphi(k+\operatorname{Bin}(n-j,p_+)),\qquad j=0,\ldots,n.$$

These functions are increasing. If $K_j$ is the count of the first $j$ bits, then the conditional probability lower bound implies

$$\mathbb E[H_j(K_j)\mid\mathcal F_{j-1}]\ge H_{j-1}(K_{j-1}).$$

Iterating yields $\mathbb E\varphi(K_n)\ge\mathbb E\varphi(\operatorname{Bin}(n,p_+))$. Apply this with $\varphi=-f$. The argument also works conditionally on the initial full history of a block, and for every later fresh group of $n$ bits within that block. Its reversed inequality proves the lower-tail comparison.

The bounded block increments and uniform inward drift are exactly the inputs to the audited exponential argument in Section 7.5, with conditional expectations now taken on the full filtration. Hence from every finite deterministic initial log amplitude and admissible initial history,

$$\sup_t\mathbb E e^{\theta|x_t|}<\infty,\qquad x_t/t\longrightarrow0\quad\text{almost surely}$$

for some fixed $\theta>0$. All pathwise finite-time identities and the asymptotic sharp rate envelope in Section 7.6 follow. The martingale strong law transfers the envelope to average conditional risk. The exact $w=2$ empirical-rate identity and quantitative corollary also follow under this weaker response condition.

**Required qualifications.** This is stability of the logarithmic relaxation amplitude, not a proof of full plant stability or recursive MPC feasibility. There is no invariant-law existence claim for arbitrary history-dependent dynamics; that requires a separately specified suitable full-state Markov model. Nor does the tail-only assumption automatically make stationary multiplicative bias strict when zero feedback lies on the count lattice: behavior inside the compact interval may be deterministic and maintain $b_t=0$. The weak envelope remains valid, and the algebraic noninteger-lattice upper bound remains strict. Finally, the tail implications must hold uniformly almost surely at every time; occasional or average tail responsiveness is a different hypothesis that this proof does not cover.

## 9. Final main-report check and freeze

The main report `数学性质研究与完整证明.md` was independently read in full before insertion of the final history-dependent extension. No substantive mathematical errors were found in its existing finite hitting-time, invariant-law, geometric-tail, optimal-order, calibration, concentration, stationary-bias, existence, or impossibility proofs. The root author was advised to update its result map and closing scope discussion when inserting the extension: the actual-update guarantee now needs uniform tail response conditions, and no longer requires retained-count probability sufficiency or the scalar response model. The abstract probability-controller theorem still has its own distinct retained-count assumption. This concludes the requested proof audit; the original manuscript remains unmodified.
