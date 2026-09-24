# 原论文乘法更新：可证明的新性质

日期：2026-09-08。分析对象仅为论文公式 `update h new`，不使用本地旧 MATLAB 代码。本文是理论探索记录，未修改论文。

## 0. 统一记号与结果范围

固定一个约束，省略约束下标。固定窗口 $w\ge2$，固定增益分母 $\chi>0$，定义

\[
q_t=-h_t>0,\qquad x_t=\log q_t,\qquad n=w-1,\qquad c=\frac{w-1}{w},
\]
\[
A=\alpha-\frac1{2w},\qquad B=1-\alpha-\frac1{2w},\qquad
a=\frac{w\alpha-1/2}{w-1}=\alpha+\frac{\alpha-1/2}{w-1}.
\]

The letters $A,B$ are positive in the interior regime $1/(2w)<\alpha<1-1/(2w)$. Unless otherwise stated the update factor is assumed positive. In that interior regime the exact sufficient and worst-case necessary condition is $\chi>B$.

Let $\mathcal F_t$ contain all observations and controller quantities through time $t$. The next violation probability is $p_{t+1}=\mathbb P(O_{t+1}=1\mid\mathcal F_t)$. No independence assumption is used for the algebraic and martingale results below.

The principal distinctions are:

1. Exact identities and finite-time certificates apply to the paper's actual multiplicative update, for any binary observation sequence.
2. The invariant-distribution theorem adds a stated scalar probabilistic response assumption linking $h$ to violations; that assumption is not proved by the MPC formulation.
3. None of these results asserts convergence of a fixed-length window to a constant under persistent nondegenerate noise.

## 1. Cancellation of the outgoing observation and exact admissible feedback range

**Lemma 1.** Put $S_t=\sum_{j=0}^{n-1}O_{t-j}$. The paper's feedback numerator satisfies the exact identity

\[
b_t=\alpha-Y_t^w+\frac{2O_{t-w+1}-1}{2w}
=A-\frac{S_t}{w}=c\left(a-\frac{S_t}{n}\right).
\tag{1}
\]

Consequently $b_t\in\{A-k/w:k=0,\ldots,n\}\subset[-B,A]$, and

\[
x_t-x_{t-1}=f(S_t),\qquad
f(k)=\log\left(1+\frac{A-k/w}{\chi}\right).
\tag{2}
\]

**Proof.** The term $O_{t-w+1}/w$ in the correction cancels the same term in $Y_t^w$. The remaining sum consists of the latest $w-1$ bits. Its minimum and maximum are 0 and $w-1$. Taking the logarithm of the positive multiplicative factor gives (2). ∎

Thus the numerator actually uses $w-1$ observations. The correction does not merely change one-step timing: its zero-mean feedback target is $a$, which differs from $\alpha$.

**Degenerate window.** When $w=1$, $b_t=\alpha-1/2$ identically. Then

\[
q_t=q_0\left(1+\frac{\alpha-1/2}{\chi}\right)^t.
\]

There is no observation feedback; $\alpha=1/2$ freezes the parameter.

**Boundary target obstruction.** If $\alpha\le1/(2w)$, all $b_t\le0$, so $q_t$ can never increase. If $\alpha\ge1-1/(2w)$, all $b_t\ge0$, so it can never decrease. Thus the natural bidirectional regime of this particular feedback law is the strict interval above, not the entire interval $(0,1)$.

## 2. A finite-time identity without any probabilistic model

Reindex a run of $N$ updates after window warmup as $t=1,\ldots,N$; the $n-1$ preceding binary observations are given initial history. Define

\[
\bar O_N=\frac1N\sum_{t=1}^{N}O_t,
\quad R_N=\frac1N\sum_{t=1}^{N}r(b_t/\chi),
\quad r(u)=u-\log(1+u),
\]
\[
D_N=\sum_{t=1}^{N}S_t-n\sum_{t=1}^{N}O_t.
\]

**Theorem 2 (exact multiplicative calibration identity).** For every binary trajectory and every $N\ge1$,

\[
\boxed{
\bar O_N=a-\frac{\chi(x_N-x_0)}{cN}-\frac\chi cR_N-\frac{D_N}{nN}.
}
\tag{3}
\]

Moreover,

\[
R_N\ge0,\qquad |D_N|\le\frac{n(n-1)}2,
\tag{4}
\]

and therefore

\[
\bar O_N\le a-\frac{\chi(x_N-x_0)}{cN}+\frac{w-2}{2N}.
\tag{5}
\]

**Proof.** From $\log(1+u)=u-r(u)$, telescoping (2) gives

\[
x_N-x_0=\frac1\chi\sum_{t=1}^{N}b_t-NR_N.
\]

Equation (1) and the definition of $D_N$ give

\[
\frac1N\sum_{t=1}^{N}b_t=c(a-\bar O_N)-\frac{D_N}{wN}.
\]

Combining and using $cw=n$ yields (3). The inequality $r(u)\ge0$ is the standard inequality $\log(1+u)\le u$, with equality exactly at $u=0$.

Finally,

\[
D_N=\sum_{j=1}^{n-1}\left(\sum_{t=1}^{N}O_{t-j}-\sum_{t=1}^{N}O_t\right).
\]

For each $j$, the two shifted intervals lose at most $j$ observations and gain at most $j$ observations. Since all observations are in $[0,1]$, the absolute difference is at most $j$, also when $N<j$. Summing gives (4), and dropping the nonpositive remainder term gives (5). ∎

**Corollary 2a (pathwise long-run upper calibration).** If

\[
\liminf_{N\to\infty}\frac{x_N-x_0}{N}\ge0,
\tag{6}
\]

then $\limsup_N\bar O_N\le a$. In particular this follows if $x_N/N\to0$. When $\alpha\le1/2$, $a\le\alpha$, so the long-run violation fraction is at most the stated target. Condition (6) is a real stability condition: it excludes exponentially collapsing relaxation magnitude. It is not implied by the update algebra alone.

**Corollary 2b (diagnostic decomposition).** The discrepancy between the measured long-run rate and $\alpha$ consists of four distinct terms:

\[
\bar O_N-\alpha=
\frac{\alpha-1/2}{w-1}
-\frac{\chi}{cN}\log\frac{q_N}{q_0}
-\frac\chi cR_N
-\frac{D_N}{nN}.
\tag{7}
\]

They quantify a finite-window target shift, a parameter-drift term, a nonnegative multiplicative-curvature penalty, and a finite-history boundary term. This identity is a potentially useful theorem and a direct numerical audit equation for paper experiments.

## 3. Finite-time average conditional-risk certificate

Let $p_t=\mathbb E(O_t\mid\mathcal F_{t-1})$ and $\bar p_N=N^{-1}\sum_{t=1}^{N}p_t$. The windows may overlap arbitrarily and the MPC may depend on the entire past.

**Theorem 3.** For every fixed $N\ge1$ and $\delta\in(0,1)$, with probability at least $1-\delta$,

\[
\boxed{
\bar p_N\le
a-\frac{\chi(x_N-x_0)}{cN}
-\frac\chi cR_N
+\frac{w-2}{2N}
+\sqrt{\frac{\log(1/\delta)}{2N}}.
}
\tag{8}
\]

If the trajectory additionally satisfies $x_N-x_0\ge-L_N$, this implies

\[
\bar p_N\le a+\frac{\chi L_N}{cN}+\frac{w-2}{2N}
+\sqrt{\frac{\log(1/\delta)}{2N}}.
\tag{9}
\]

The high-probability event in (8) implies (9) whenever the stated endpoint condition holds. If the endpoint condition itself holds only with probability $1-\eta$, the unconditional guarantee from (9) is at least $1-\delta-\eta$.

**Proof.** The differences $p_t-O_t$ have conditional mean zero and lie in an interval of length one. Conditional Hoeffding's lemma and induction give

\[
\mathbb E\exp\left(\lambda\sum_{t=1}^{N}(p_t-O_t)\right)
\le\exp(N\lambda^2/8).
\]

Exponential Markov inequality followed by minimization in $\lambda>0$ yields

\[
\mathbb P(\bar p_N-\bar O_N>u)\le e^{-2Nu^2}.
\]

Substitute (3), bound the boundary term by (4), and choose $u=\sqrt{\log(1/\delta)/(2N)}$. ∎

This is an average conditional-risk guarantee. It is neither a bound on each $p_t$, nor a claim that every moving window stays below $\alpha$. It is a fixed-$N$ statement; uniform-over-time confidence requires an additional union bound or confidence-sequence argument.

## 4. Stationary calibration and a strict finite-gain bias

**Theorem 4.** Suppose the paper recurrence admits a strictly stationary process $(x_t,O_t)_{t\in\mathbb Z}$ with $\mathbb E|x_t|<\infty$. Let $\mu=\mathbb E O_t$. Then

\[
\boxed{
\mu=a-\frac\chi c\mathbb E\,r(b_t/\chi)\le a.
}
\tag{10}
\]

Equality holds if and only if $b_t=0$ almost surely. If $\mathbb P(b_t\ne0)>0$, then $\mu<a$.

**Proof.** Stationarity and integrability imply $\mathbb E(x_t-x_{t-1})=0$. Equation (2) then implies

\[
0=\frac{\mathbb E b_t}{\chi}-\mathbb E r(b_t/\chi).
\]

Stationarity of the bits yields $\mathbb E S_t=n\mu$, so (1) gives $\mathbb E b_t=c(a-\mu)$. Substitute and use strictness of the logarithm inequality. ∎

For $\alpha<1/2$, the stationary mean is strictly below $\alpha$ whenever noise makes $b_t$ nonconstant. Thus, within the stated stationary regime, exact mean calibration to $\alpha$ is not a property of this unmodified constant-gain recurrence.

**Magnitude bound.** In the interior regime $\chi>B$, set $d=a-\mu\ge0$. Then

\[
\frac{\mathbb E b_t^2}{2c(\chi+A)}
\le d\le
\frac{\mathbb E b_t^2}{2c(\chi-B)}
\le\frac{\max(A,B)^2}{2c(\chi-B)}.
\tag{11}
\]

Indeed, for $u>-1$,

\[
r(u)=u^2\int_0^1\frac{s}{1+su}\,ds.
\]

Since $u\in[-B/\chi,A/\chi]$, the integral lies between $1/[2(1+A/\chi)]$ and $1/[2(1-B/\chi)]$. Substitute into (10).

If furthermore $p_t\in[\varepsilon,1-\varepsilon]$ almost surely for some $0<\varepsilon\le1/2$, then conditioning on $\mathcal F_{t-1}$ gives

\[
\operatorname{Var}(b_t\mid\mathcal F_{t-1})=\frac{p_t(1-p_t)}{w^2}
\ge\frac{\varepsilon(1-\varepsilon)}{w^2}.
\]

Hence

\[
a-\mu\ge\frac{\varepsilon(1-\varepsilon)}{2c(\chi+A)w^2}>0.
\tag{12}
\]

Equation (12) is only a conservative lower bound, not an asserted scaling law for the actual bias.

**Almost-sure stationary version.** Birkhoff's theorem gives a sample-average limit, possibly random if the invariant law is not ergodic. Since $x_t$ is stationary and integrable,

\[
\sum_{N\ge1}\mathbb P(|x_N|>\epsilon N)<\infty
\]

by the integrable-tail criterion; thus $x_N/N\to0$ almost surely. Applying (3) and Birkhoff,

\[
\lim_N\bar O_N
=a-\frac\chi c\mathbb E[r(b_t/\chi)\mid\mathcal I]\le a
\quad\text{almost surely},
\tag{13}
\]

where $\mathcal I$ is the invariant sigma-field. The martingale strong law also makes the average of $p_t$ have the same limit. No claim of ergodicity is needed for this statement.

## 5. Sharp algebraic stationary-rate envelope

The fact that $b_t$ takes only $w$ values gives stronger bounds than Jensen alone.

Let $f_k=f(k)$, with $f_0>0>f_n$, and let $s=w\alpha-1/2\in(0,n)$. Define

\[
\mu_{\mathrm{lo}}=\frac{f_0}{f_0-f_n}.
\]

If $s\notin\mathbb Z$, put $j=\lfloor s\rfloor$, so $f_j>0>f_{j+1}$, and define

\[
\mu_{\mathrm{hi}}=\frac1n\left(j+\frac{f_j}{f_j-f_{j+1}}\right).
\]

If $s\in\mathbb Z$, define $\mu_{\mathrm{hi}}=s/n=a$.

**Theorem 5.** Under the stationary assumptions of Theorem 4,

\[
\boxed{\mu_{\mathrm{lo}}\le\mu\le\mu_{\mathrm{hi}}\le a.}
\tag{14}
\]

When $s\notin\mathbb Z$, $\mu_{\mathrm{hi}}<a$ irrespective of the probabilistic response law. These are sharp among all probability distributions of the count $S_t\in\{0,\ldots,n\}$ satisfying the single balance equation $\mathbb Ef(S_t)=0$. They need not be achievable by every dynamically consistent overlapping-window process.

**Proof.** Define the strictly concave inverse-coordinate map

\[
H(z)=w[A-\chi(e^z-1)].
\]

For $z=f_k$, $H(z)=k$, and $\mathbb Ef(S_t)=0$. The chord joining $(f_n,n)$ and $(f_0,0)$ lies below $H$ on their interval. Taking expectations gives $\mathbb ES_t\ge n f_0/(f_0-f_n)$.

For the upper bound with noninteger $s$, consider the line joining the neighboring points $(f_{j+1},j+1)$ and $(f_j,j)$. By concavity of $H$, its extension lies above $H$ outside that interval. Every allowed discrete value $f_k$ is either one endpoint or outside the open interval; therefore this line majorizes $H$ on the entire allowed discrete support. Taking expectations gives $\mathbb ES_t\le j+f_j/(f_j-f_{j+1})$. Strict concavity makes the line's value at zero strictly less than $H(0)=s$. If $s$ is an integer, Jensen directly gives $\mathbb ES_t=\mathbb EH(f(S_t))\le H(0)=s$.

The lower bound is attained for distributions supported on $\{0,n\}$ with zero expected $f$; the upper bound is attained on $\{j,j+1\}$, or at the deterministic $s$ if it is integral. This proves the stated algebraic sharpness only. Finally use $\mathbb ES_t=n\mu$. ∎

**Special exact case $w=2$.** There are only two count values, so the bounds coincide:

\[
\mu=\frac{\log(1+(\alpha-1/4)/\chi)}{
\log(1+(\alpha-1/4)/\chi)-\log(1+(\alpha-3/4)/\chi)}.
\tag{15}
\]

Thus any integrable stationary law of the actual $w=2$ recurrence has exactly this mean violation rate, independent of the shape of the response map that produced it.

## 6. Existence of a stable stationary regime under an explicit response assumption

This section supplies a noncircular sufficient condition for existence of the stationary regime used above.

**Response assumption.** There is a continuous function $g:\mathbb R\to(0,1)$ such that

\[
\mathbb P(O_{t+1}=1\mid\mathcal F_t)=g(x_t),\qquad
\lim_{x\to-\infty}g(x)=0,\qquad
\lim_{x\to+\infty}g(x)=1.
\tag{16}
\]

The conditional law depends on the current logarithmic relaxation magnitude, with the entire state and forecasting mechanism summarized by this scalar response. The limit conditions encode inward negative feedback for sufficiently small/large relaxation. Monotonicity is physically natural but not needed for the proof. This is a substantive modeling assumption; the general MPC optimization does not establish it, and simultaneous interaction between several constraints can violate scalar sufficiency. It can be used first for a scalar reduced model or justified with separate response experiments.

**Theorem 6 (existence with integrable log amplitude).** Suppose $w\ge2$, $1/(2w)<\alpha<1-1/(2w)$, $\chi>B$, and (16) holds. Then the Markov chain

\[
Z_t=(x_t,O_{t-n+1},\ldots,O_t)\in\mathbb R\times\{0,1\}^{n}
\]

has at least one invariant probability measure $\pi$ satisfying $\int|x|\,d\pi<\infty$. Under that invariant law the mean violation satisfies the strict inequality

\[
\mu<a.
\tag{17}
\]

No uniqueness of $\pi$, convergence in distribution from every initial condition, or pathwise convergence of $q_t$ is asserted.

**Proof.** Let $L=\max_{0\le k\le n}|f_k|>0$. Every log-amplitude increment has magnitude at most $L$. Write $u=f_0>0$ and $v=-f_n>0$.

Choose an integer block length $m\ge n$ so large that

\[
d_+=(m-n+1)v-(n-1)L>0,
\qquad d_-=(m-n+1)u-(n-1)L>0.
\]

Let $d=\min(d_+,d_-)>0$, $M=mL$, and choose $\epsilon>0$ small enough that

\[
m\epsilon(d+M)\le d/2.
\]

By the limits of $g$, choose $R>0$ such that $g(x)\ge1-\epsilon$ for $x\ge R$ and $g(x)\le\epsilon$ for $x\le-R$. If $x_t\ge R+M$, bounded increments keep all the next $m$ generating amplitudes above $R$. Conditional on any earlier bits in that block, each new bit is 1 with probability at least $1-\epsilon$. Hence the conditional probability that all $m$ new bits equal 1 is at least $1-m\epsilon$, by the conditional union bound.

On the all-ones event, the first $n-1$ increments are at most $L$, and each of the remaining $m-n+1$ increments equals $-v$. Thus $x_{t+m}-x_t\le-d_+\le-d$. Off that event it is at most $M$. Consequently

\[
\mathbb E(x_{t+m}-x_t\mid Z_t)\le-(1-m\epsilon)d+m\epsilon M\le-d/2.
\tag{18}
\]

Similarly, if $x_t\le-R-M$, the all-zeros event has probability at least $1-m\epsilon$ and gives

\[
\mathbb E(x_{t+m}-x_t\mid Z_t)\ge d/2.
\tag{19}
\]

For $V(Z)=x^2$, expand $V(Z_{t+m})-V(Z_t)=2x_t\Delta x+(\Delta x)^2$. Using (18)–(19) and $|\Delta x|\le M$, outside the compact interval we obtain

\[
\mathbb E[V(Z_{t+m})-V(Z_t)\mid Z_t]\le-d|x_t|+M^2.
\]

On the remaining compact interval the same left-hand side is uniformly bounded by $2(R+M)M+M^2$. Therefore there is a finite constant $C$ such that globally

\[
\mathbb E[V(Z_{t+m})-V(Z_t)\mid Z_t]\le-d|x_t|+C.
\tag{20}
\]

For any deterministic initial state, sum (20) along the $m$-step skeleton. Since $V\ge0$,

\[
\frac1K\sum_{j=0}^{K-1}\mathbb E|x_{jm}|
\le\frac C d+\frac{x_0^2}{dK}.
\tag{21}
\]

The Cesàro occupation measures of the skeleton are thus tight, because the memory space is finite and Markov's inequality controls the $x$ coordinate. The one-step transition is Feller: it is a two-branch update, each branch is continuous in $x$, with continuous probabilities $g(x)$ and $1-g(x)$; the memory space has the discrete topology. Hence $P^m$ is Feller as well. A weakly convergent subsequence of the Cesàro measures has limit $\nu$, and the telescoping relation between their images under $P^m$ shows $\nu P^m=\nu$. Lower semicontinuity of $|x|$ and (21) give $\int|x|\,d\nu\le C/d$.

Define

\[
\pi=\frac1m\sum_{r=0}^{m-1}\nu P^r.
\]

Then $\pi P=\pi$, using $\nu P^m=\nu$. Bounded one-step increments preserve the finite first moment, so $\int|x|d\pi<\infty$.

It remains to establish strictness. For every finite state, assumption (16) gives strictly positive probability to any specified finite sequence of zeros and ones. In particular, conditional probabilities of $n$ successive zeros and of $n$ successive ones are strictly positive. Under an invariant law, integrating these strictly positive probabilities proves $\pi(S_t=0)>0$ and $\pi(S_t=n)>0$. Since $A>0$ and $-B<0$, $b_t\ne0$ with positive probability. Theorem 4 now proves (17). ∎

**Why the target restriction is necessary in this model.** If $\alpha\le1/(2w)$, every increment $f(S_t)\le0$, and some strictly negative increment occurs with positive stationary probability under (16). A stationary integrable $x_t$ would then have a strictly negative expected increment, a contradiction. The upper boundary case is symmetric. Even without integrability, equality in distribution of two ordered real random variables implies almost-sure equality, so a proper stationary law cannot support a strict one-directional increment. The model therefore has no invariant probability measure in either exterior regime.

## 6A. Stronger stability and actual-algorithm guarantees from arbitrary initial conditions

The following strengthening was independently checked by the proof auditor after Theorem 6.

**Theorem 6a (uniform exponential log-amplitude moment).** Under the assumptions of Theorem 6, for every deterministic finite initial log-amplitude and binary initial memory, there are constants $\theta>0$ and $C_0<\infty$ such that

\[
\sup_{t\ge0}\mathbb E e^{\theta|x_t|}\le C_0.
\tag{23}
\]

Consequently $x_t/t\to0$ almost surely. The constants may depend on the response map, window, gain, target, and initial amplitude; they are not claimed to be practically sharp.

**Proof.** Retain the constants $m,M,d,R$ constructed in Theorem 6. Outside $|x_t|\ge R+M$, the sign cannot change over the next block because $|\Delta x|\le M$ and $R>0$. Put $D=\operatorname{sign}(x_t)(x_{t+m}-x_t)$. Then $|D|\le M$, $\mathbb E(D\mid Z_t)\le-d/2$, and $|x_{t+m}|-|x_t|=D$.

Taylor's formula gives

\[
e^{\theta D}\le1+\theta D+\frac{\theta^2M^2e^{\theta M}}2.
\]

Choose $\theta>0$ sufficiently small that $\theta M^2e^{\theta M}\le d/2$ and $\theta d/4<1$. For $W(Z)=e^{\theta|x|}$ and $\lambda=1-\theta d/4\in(0,1)$,

\[
P^mW(Z)\le\lambda W(Z)\qquad (|x|\ge R+M).
\]

Inside $|x|\le R+M$, bounded increments give $P^mW(Z)\le K:=e^{\theta(R+2M)}$. Thus globally $P^mW\le\lambda W+K$, and iteration yields

\[
\mathbb EW(Z_{jm})\le W(Z_0)+\frac K{1-\lambda}.
\]

For intermediate indices $jm+r$, $0\le r<m$, the bound $|x_{jm+r}-x_{jm}|\le M$ contributes at most a factor $e^{\theta M}$. This proves (23).

For every $\epsilon>0$, Markov's inequality gives $\mathbb P(|x_t|>\epsilon t)\le C_0e^{-\theta\epsilon t}$, a summable sequence. Borel--Cantelli and then a countable intersection over positive rational $\epsilon$ yield $x_t/t\to0$ almost surely. ∎

**Lemma 6b (pathwise finite-time discrete-envelope certificates).** Retain the notation of Theorem 5. For every binary trajectory of the original update,

\[
\bar O_N\ge \mu_{\mathrm{lo}}
-\frac{x_N-x_0}{N(f_0-f_n)}
-\frac{D_N}{nN}.
\tag{24}
\]

If $s=w\alpha-1/2$ is not an integer, then also

\[
\bar O_N\le \mu_{\mathrm{hi}}
-\frac{x_N-x_0}{nN(f_j-f_{j+1})}
-\frac{D_N}{nN}.
\tag{25}
\]

If $s$ is an integer, then

\[
\bar O_N\le a-\frac{\chi(x_N-x_0)}{cN}-\frac{D_N}{nN}.
\tag{26}
\]

**Proof.** For the inverse function $H$ in Theorem 5, the global endpoint chord gives

\[
k\ge n\mu_{\mathrm{lo}}-\frac{n}{f_0-f_n}f_k.
\]

The discrete-support majorizing chord gives, when $s$ is noninteger,

\[
k\le n\mu_{\mathrm{hi}}-\frac1{f_j-f_{j+1}}f_k.
\]

If $s$ is an integer, the tangent to the concave $H$ at zero gives $k\le s-w\chi f_k$. Substitute $k=S_t$, sum, use $\sum f(S_t)=x_N-x_0$ and $\sum S_t=nN\bar O_N+D_N$, and divide by $nN$. ∎

**Corollary 6c (long-run risk envelope of the actual algorithm).** Under the response assumptions of Theorem 6, from every finite deterministic initial state,

\[
\boxed{
\mu_{\mathrm{lo}}\le\liminf_{N\to\infty}\bar O_N
\le\limsup_{N\to\infty}\bar O_N\le\mu_{\mathrm{hi}}\le a
\quad\text{almost surely}.
}
\tag{27}
\]

The same bounds apply to the average conditional risks $\bar p_N$. If $\alpha\le1/2$, their upper bound is at most $\alpha$. This does not assume stationary initialization, uniqueness of an invariant law, or convergence of the general-$w$ empirical rate.

**Proof.** Theorem 6a makes the log-amplitude terms in (24)–(26) vanish almost surely. The history term vanishes by (4). The bounded martingale differences $O_t-p_t$ have vanishing average almost surely, by the two-sided conditional Hoeffding bound and Borel--Cantelli. ∎

**Corollary 6d (exact $w=2$ limit and an $O(1/N)$ mean error).** If $w=2$ and the response assumptions hold, set

\[
\mu_2=\frac{f_0}{f_0-f_1},\qquad J=f_0-f_1>0.
\]

Then, from every finite deterministic initial state,

\[
\bar O_N-\mu_2=-\frac{x_N-x_0}{JN}
\quad\text{for every }N,
\tag{28}
\]

and therefore

\[
\bar O_N\longrightarrow\mu_2\quad\text{almost surely},
\qquad
\mathbb E|\bar O_N-\mu_2|
\le \frac{|x_0|+\theta^{-1}\log C_0}{JN}.
\tag{29}
\]

For all $u>0$,

\[
\mathbb P(|\bar O_N-\mu_2|>u)
\le \min\{1,\ C_0e^{\theta|x_0|-\theta JNu}\}.
\tag{30}
\]

Also $|\bar O_N-\mu_2|=O(\log N/N)$ almost surely. The mean rate $\mu_2$ has the explicit formula (15). The result concerns empirical violations; replacing them by conditional probabilities introduces a martingale average and does not inherit the same $O(1/N)$ mean-error bound from this proof.

**Proof.** When $n=1$, $S_t=O_t$, so $f(S_t)=f_0+(f_1-f_0)O_t$. Sum (2) and solve to obtain (28). Theorem 6a gives almost-sure convergence, and Jensen's inequality gives $\mathbb E|x_N|\le\theta^{-1}\log\mathbb Ee^{\theta|x_N|}\le\theta^{-1}\log C_0$, proving (29). Equation (28), the triangle inequality, and exponential Markov inequality yield (30); when $JNu\le|x_0|$ its untruncated right-hand side is at least one, so the stated bound remains valid. Finally choose any $b>1/\theta$. The probabilities $\mathbb P(|x_N|>b\log N)\le C_0N^{-\theta b}$ are summable for $N\ge2$. Borel--Cantelli and (28) prove the almost-sure rate. ∎

This $w=2$ result is an exact nontrivial closed-loop theorem for the paper recurrence, but the small window is a mathematical special case rather than the experimental setting.

## 6B. Weaker response limits: residual baseline risk and incomplete attainability

The tail limits 0 and 1 in (16) can be relaxed substantially. This extension is more plausible when even the nominal constraint has a nonzero violation probability.

Define the frozen-probability log-drift function

\[
\Psi(p)=\sum_{k=0}^{n}\binom nk p^k(1-p)^{n-k}f_k,
\qquad p\in[0,1].
\tag{31}
\]

**Lemma 6e.** The function $\Psi$ is continuous and strictly decreasing, with a unique zero $p_\dagger\in(0,a)$.

**Proof.** It is a polynomial in $p$. For $p_1<p_2$, use common independent uniforms to generate $n$ Bernoulli variables at each probability. Their counts satisfy $K_1\le K_2$ almost surely and $\mathbb P(K_1<K_2)>0$. Since $f$ is strictly decreasing, $\mathbb Ef(K_1)>\mathbb Ef(K_2)$. Also $\Psi(0)=f_0>0$ and $\Psi(1)=f_n<0$, so the unique zero is in $(0,1)$. At $p=a$, $\mathbb E(A-K/w)=0$, and $K\sim\operatorname{Bin}(n,a)$ is nonconstant because $n\ge1$ and $a\in(0,1)$. Strict concavity of the logarithm gives $\Psi(a)<0$. Thus $p_\dagger<a$. ∎

This number is the zero of a frozen independent-Bernoulli log drift. It is not asserted to equal the stationary mean of the adaptive process when $w>2$; that process has temporal dependence.

**Theorem 6f (stability with an attainable tail interval).** Replace the limits in (16) by

\[
\lim_{x\to-\infty}g(x)=p_-,
\qquad
\lim_{x\to+\infty}g(x)=p_+,
\qquad
0\le p_-<p_\dagger<p_+\le1.
\tag{32}
\]

Retain continuity, $g(x)\in(0,1)$ at every finite $x$, the scalar conditional-response relation, and the target and gain conditions from Theorem 6. Then all conclusions of Theorem 6, Theorem 6a, and Corollaries 6c--6d remain valid.

**Proof.** Put $\beta=\min\{\Psi(p_-),-\Psi(p_+)\}>0$, and retain $L=\max_k|f_k|$. Choose $\epsilon>0$ with $2Ln\epsilon\le\beta/2$. Choose $R>0$ so that $|g(x)-p_+|\le\epsilon$ for $x\ge R$ and $|g(x)-p_-|\le\epsilon$ for $x\le-R$. Choose $m\ge n$ sufficiently large that

\[
d=(m-n+1)\beta/2-(n-1)L>0,\qquad M=mL.
\]

Starting at $x_t\ge R+M$, all the next $m$ observation-generating states stay above $R$. Realize the Bernoulli transition using independent uniforms $U_{t+1},\ldots,U_{t+m}$:
$O_{t+r}=\mathbf1\{U_{t+r}\le g(x_{t+r-1})\}$.
This construction has exactly the assumed conditional law. Define the comparison bits $\widetilde O_{t+r}=\mathbf1\{U_{t+r}\le p_+\}$.

Each actual/comparison pair has conditional mismatch probability at most $\epsilon$. For each $j\ge n$, the window $S_{t+j}$ contains only observations generated within this block. By the union bound its $n$ bits differ from the corresponding comparison window with probability at most $n\epsilon$, conditional on $Z_t$. The comparison count has the distribution $\operatorname{Bin}(n,p_+)$, and $|f_k|\le L$. Thus

\[
\left|\mathbb E[f(S_{t+j})\mid Z_t]-\Psi(p_+)\right|
\le2Ln\epsilon\le\beta/2.
\]

It follows that $\mathbb E[f(S_{t+j})\mid Z_t]\le-\beta/2$ for $j=n,\ldots,m$. The first $n-1$ increments are at most $L$, hence

\[
\mathbb E(x_{t+m}-x_t\mid Z_t)
\le(n-1)L-(m-n+1)\beta/2=-d.
\]

The same construction with comparison probability $p_-$, starting below $-R-M$, yields an expected block increment at least $d$. These are precisely bounded block increments and uniform inward tail drift, the two ingredients used in the quadratic invariant-measure proof and the exponential-moment proof. Substituting this positive $d$ in those arguments proves existence and uniform exponential moments. The purely algebraic and martingale corollaries then apply without change. Strict stationary bias still follows because $g(x)\in(0,1)$ for every finite state makes both constant finite bit strings have positive probability. ∎

Thus a checkable sufficient condition is that the response's lower and upper tail probabilities straddle the explicit root of (31). The proof does not need deterministic control of either violation outcome, global monotonicity of $g$, or attainment of probabilities 0 and 1.

## 6C. Strongest response theorem: history dependence without a scalar model

This is the final extension in this exploration. Its proof was independently audited.

**Theorem 6g (history-dependent response: actual-algorithm risk envelope).** Let $p_{t+1}=\mathbb E(O_{t+1}\mid\mathcal F_t)$ depend arbitrarily on the entire history. Retain $w\ge2$, $1/(2w)<\alpha<1-1/(2w)$, and $\chi>B$. Let $p_\dagger$ denote the unique zero from Lemma 6e. Assume there exist constants $R>0$ and $0\le p_-<p_\dagger<p_+\le1$ such that, almost surely at every time,

\[
x_t\le-R\ \Longrightarrow\ p_{t+1}\le p_-,
\qquad
x_t\ge R\ \Longrightarrow\ p_{t+1}\ge p_+.
\tag{33}
\]

The inequalities must hold uniformly over admissible histories in the corresponding tail. Then, from every finite deterministic initial amplitude and binary initial memory, the uniform exponential moment (23), the almost-sure risk envelope (27), and the exact $w=2$ conclusions (28)--(30) hold. No scalar response map, global response monotonicity, Markov assumption, or continuity is required.

This theorem concerns the relaxation amplitude and violation averages. It does not establish boundedness of the full plant state, recursive MPC feasibility, or an invariant distribution of the full closed loop. It does not inherit strict stationary bias automatically: the general history-dependent model may permit a deterministic zero-drift trajectory inside a compact region. The discrete strict upper bound $\mu_{\mathrm{hi}}<a$ when $w\alpha-1/2$ is noninteger remains valid.

**Proof.** Put $u=\Psi(p_-)>0$, $v=-\Psi(p_+)>0$, and $L=\max_k|f_k|$. Choose $m\ge n$ sufficiently large that

\[
d=\min\{(m-n+1)u-(n-1)L,\ (m-n+1)v-(n-1)L\}>0,
\qquad M=mL.
\]

Starting at $x_t\ge R+M$, bounded increments keep all of the next $m$ observation-generating amplitudes at least $R$. Every conditional violation probability in this block is therefore at least $p_+$.

We use the following conditional stochastic-domination fact. If $B_1,\ldots,B_n$ are adapted Bernoulli variables whose conditional success probabilities are at least $p$, then for every increasing function $\varphi$ on $\{0,\ldots,n\}$,

\[
\mathbb E\!\left[\varphi\!\left(\sum_{\ell=1}^{n}B_\ell\right)\,\middle|\,\text{initial history}\right]
\ge \mathbb E\varphi(K),\qquad K\sim\operatorname{Bin}(n,p).
\tag{34}
\]

For a self-contained proof, write $K_j=\sum_{\ell=1}^{j}B_\ell$ and
$H_j(k)=\mathbb E\varphi(k+\operatorname{Bin}(n-j,p))$.
Each $H_j$ is increasing and obeys
$H_{j-1}(k)=(1-p)H_j(k)+pH_j(k+1)$.
If $q_j$ is the actual conditional success probability, then

\[
\mathbb E[H_j(K_j)\mid\text{history through }j-1]
=(1-q_j)H_j(K_{j-1})+q_jH_j(K_{j-1}+1)
\ge H_{j-1}(K_{j-1}).
\]

Iterated conditional expectation proves (34). The same argument with all inequalities reversed applies when conditional probabilities are at most $p$. The lemma also applies on an event known at the initial history, as here.

For every $j=n,\ldots,m$, the count $S_{t+j}$ contains only fresh observations in the block. Apply (34) to those $n$ bits with $\varphi=-f$, which is increasing. Conditional first on the history at the start of those bits and then on $\mathcal F_t$, this gives

\[
\mathbb E[f(S_{t+j})\mid\mathcal F_t]\le\Psi(p_+)=-v
\quad\text{on }\{x_t\ge R+M\}.
\]

The first $n-1$ increments are at most $L$. Summing yields

\[
\mathbb E(x_{t+m}-x_t\mid\mathcal F_t)
\le(n-1)L-(m-n+1)v\le-d.
\tag{35}
\]

For $x_t\le-R-M$, conditional probabilities in the block are at most $p_-$. The reversed domination makes each fresh-window expected increment at least $u$. Hence the total expected block increment is at least $d$.

These conditional block bounds and $|x_{t+m}-x_t|\le M$ are exactly the ingredients used in Theorem 6a. That proof remains valid with conditional expectations given $\mathcal F_t$ instead of a Markov transition operator: the exponential Taylor estimate gives $\mathbb E(W_{t+m}\mid\mathcal F_t)\le\lambda W_t+K$, iteration proves (23), and Borel--Cantelli yields $x_t/t\to0$ almost surely. The pathwise envelope and martingale proofs then give (27)--(30). ∎

The uniform conditional inequalities (33) require separate justification for the MPC. An average empirical response curve alone does not prove uniformity over histories. The theorem permits dependence on plant state, forecasts, earlier violations, and other constraint coordinates, provided the tail inequalities remain valid.

## 7. Persistent noise prevents exact convergence of the moving window

**Theorem 7.** Suppose $w<\infty$, and for all times $p_{t+1}\in[\varepsilon,1-\varepsilon]$ almost surely, where $\varepsilon>0$. Then almost surely $Y_t^w=0$ infinitely often and $Y_t^w=1$ infinitely often. Therefore for every $\alpha\in(0,1)$, $Y_t^w\not\to\alpha$, and

\[
\limsup_{t\to\infty}|Y_t^w-\alpha|=\max(\alpha,1-\alpha)
\quad\text{almost surely}.
\tag{22}
\]

**Proof.** Partition future observations into disjoint blocks of length $w$. Conditional on the history at a block boundary, repeated conditioning gives probability at least $\varepsilon^w$ that the next block is entirely zero. Consequently the conditional probability that none of the next $K$ blocks is entirely zero is at most $(1-\varepsilon^w)^K\to0$. This argument applies after every deterministic block index. A countable intersection shows there is no last all-zero block, almost surely. Apply the same argument to all-one blocks. At their endpoints the moving averages equal 0 and 1 respectively. The claimed limsup follows since every moving average is in $[0,1]$. ∎

For large $w$, these extreme runs can be extraordinarily rare. The theorem is a mathematical obstruction to an asymptotic exact-convergence claim, not a claim that such extremes are likely within the simulated experiment duration. The uniform-noise assumption is separate from (16), whose tail probabilities approach 0 and 1; Theorem 7 is applied only when its own condition holds.

## 8. Suggested paper-facing choice

The strongest direct additions appear to be: (i) the exact calibration identity (3) with the average-risk certificate (8); (ii) stationary downward bias (10), emphasizing the two distinct sources of shift; and (iii) the response-model stability theorem (Theorem 6), if the additional scalar response model is acceptable. The sharp discrete envelope is a further structural result and the $w=2$ case is a clean exact example. The impossibility theorem clarifies the correct convergence notion.

For the paper's reported $w=2880,\alpha=0.1$, the feedback target alone is

\[
a=0.1-\frac{0.4}{2879}\approx0.0998610628691.
\]

The additional multiplicative bias depends on the actual stationary count distribution and $\chi$. It must not be assigned a numerical value without those data.

These proofs are proposed for independent audit before integration. No theorem has been claimed for the full multivariate MPC without a separately justified probability-response link.
