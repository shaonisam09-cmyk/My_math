# A general Poisson-equation bridge to full-history MPC

Date: 2026-09-08. This is a new theoretical exploration. It does not modify the manuscript or use local MATLAB code.

## 1. Purpose and scope

The exact invariant distribution in `window_theory.md` assumes that the true conditional violation probability is a deterministic function of the retained count alone. The theorem below removes that assumption: the actual probability may depend on the full MPC state, forecast history, solver choices, all previous observations, time, and auxiliary randomness.

The price is an explicit sensitivity constant and an explicit error term measuring disagreement with the reference probability selector. This gives a valid sufficient condition for transferring the reference O(1/w) error rate. It does **not** automatically establish that the actual multiplicative update satisfies the needed probability-error condition.

## 2. Reference process and exact constants

Let X_t=(O_{t-w+1},...,O_t) be the binary window, S_t=sum_{j=t-w+2}^t O_j its retained count, and K_t=|X_t|. Let T_b x denote the window obtained by deleting the oldest bit of x and appending b in {0,1}.

Fix 0<l<u<1 and a nearest integer k to w alpha, with a fixed valid tie choice. Define the reference probability

\[
 q_s=\begin{cases}u,&s<k,\\l,&s\ge k.\end{cases}
\]

Its transition kernel is

\[
 (P H)(x)=(1-q_{S(x)})H(T_0x)+q_{S(x)}H(T_1x).
\]

The reference chain is irreducible and aperiodic and has the exact invariant law proved in `window_theory.md`. Define the tracking cost and its exact reference mean by

\[
 f(x)=\left|\frac{|x|}{w}-\alpha\right|,
 \qquad m=\pi f.
\]

For this finite reference chain, the Poisson equation

\[
 H-PH=f-m \tag{P}
\]

has a solution unique up to an additive constant. One choice is the absolutely convergent series

\[
 H(x)=\sum_{r=0}^{\infty}\bigl(P^r f(x)-m\bigr).
\]

Two shift-invariant constants will be used:

\[
 B_H=\max_x H(x)-\min_x H(x),\qquad
 D_H=\max_x|H(T_1x)-H(T_0x)|.
\]

The second maximum is over only 2^(w−1) distinct retained words, and D_H<=B_H. It is the appropriate sensitivity constant because a probability perturbation redistributes mass only between the two possible successor windows.

## 3. Full-history probability perturbations

Let F_t contain the complete actual history and the current window. The actual controller is completely arbitrary apart from producing a binary next observation with conditional probability

\[
 p_{t+1}=\Pr(O_{t+1}=1\mid\mathcal F_t).
\]

Set

\[
 e_t=p_{t+1}-q_{S_t},\qquad
 d_H(x)=H(T_1x)-H(T_0x).
\]

No Markov property, independence, stationary probability-response map, or retained-count sufficiency is imposed on the actual process. Even p_{t+1} outside [l,u] is allowed; its mismatch is then represented by e_t.

**Theorem 1 (exact perturbation identity).** For every actual trajectory and every integer N>=1,

\[
 \frac1N\sum_{t=0}^{N-1} f(X_t)-m
 =\frac{H(X_0)-H(X_N)}N
 +\frac1N\sum_{t=0}^{N-1}e_t d_H(X_t)
 +\frac1N\sum_{t=0}^{N-1}M_{t+1}, \tag{1}
\]

where

\[
 M_{t+1}=H(X_{t+1})-\mathbb E[H(X_{t+1})\mid\mathcal F_t]
\]

is a bounded martingale difference. In particular,

\[
 \left|\frac1N\sum_{t=0}^{N-1}\mathbb E f(X_t)-m\right|
 \le\frac{B_H}{N}
 +D_H\frac1N\sum_{t=0}^{N-1}\mathbb E|e_t|. \tag{2}
\]

For every fixed N and delta in (0,1), with probability at least 1−delta,

\[
 \frac1N\sum_{t=0}^{N-1}f(X_t)
 \le m+\frac{B_H}{N}
 +D_H\left(\frac1N\sum_{t=0}^{N-1}|e_t|
 +\sqrt{\frac{\log(1/\delta)}{2N}}\right). \tag{3}
\]

The empirical error average in (3) may be random; the assertion is an implication on the martingale high-probability event and needs no independence between it and that average.

**Proof.** Conditional on F_t,

\[
 \mathbb E[H(X_{t+1})\mid\mathcal F_t]
 =(PH)(X_t)+e_t d_H(X_t).
\]

Substituting into (P) gives

\[
 f(X_t)-m=H(X_t)-H(X_{t+1})+e_t d_H(X_t)+M_{t+1}.
\]

Summing proves (1); expectation, telescoping, and |d_H|<=D_H prove (2). Given F_t, H(X_{t+1}) has only two possible values separated by |d_H(X_t)|<=D_H. Therefore the centered variable M_{t+1} lies in a conditionally known interval of length at most D_H. Conditional Hoeffding's lemma gives

\[
 \Pr\left(\frac1N\sum_{t=0}^{N-1}M_{t+1}>z\right)
 \le \exp(-2Nz^2/D_H^2).
\]

Equation (3) follows from (1). If D_H=0, the martingale and perturbation terms both vanish and the displayed conclusion is interpreted directly. QED.

**Long-run pathwise consequence.** For fixed w, the bounded martingale-difference strong law gives N^(-1)sum M_{t+1}->0 almost surely. Thus

\[
 \limsup_{N\to\infty}\frac1N\sum_{t=0}^{N-1}f(X_t)
 \le m+D_H\limsup_{N\to\infty}\frac1N\sum_{t=0}^{N-1}|e_t|
 \quad\text{almost surely}. \tag{4}
\]

This controls the long-run average **window tracking error**, even for a nonstationary full-history MPC. It does not assert convergence of each moving-window average or a bound on every realized window.

## 4. Stationary marginal version

**Corollary 2.** If the actual window marginal is stationary with law mu, then at every applicable time

\[
 \mu f-\pi f=\mathbb E[e_t d_H(X_t)]. \tag{5}
\]

Consequently, if E|e_t|<=epsilon,

\[
 |\mu f-\pi f|\le D_H\epsilon. \tag{6}
\]

Only stationarity of the window marginal is needed for the boundary expectations to cancel. A stationary augmented MPC process is a sufficient condition. The actual binary-window process need not be Markov, and the mismatch may be history dependent.

The exact signed quantity in (5) can be much smaller than D_H E|e_t| because beneficial errors and cancellations are preserved in (5). Uniform error |e_t|<=epsilon is convenient but stronger than needed; an average probability-error certificate suffices.

## 5. Explicit conservative constants from minorization

Define eta=(1−u+l)^w. For every start x and target word z, generating z over the next w steps under the reference policy has probability at least l^(|z|)(1−u)^(w−|z|). Hence

\[
 P^w(x,\cdot)\ge\eta\nu(\cdot),\qquad
 \nu(z)=\frac{l^{|z|}(1-u)^{w-|z|}}{\eta}.
\]

This implies oscillation contraction

\[
 \operatorname{osc}(P^r f)
 \le(1-\eta)^{\lfloor r/w\rfloor}\operatorname{osc}(f).
\]

Summing the Poisson series gives the explicit, valid bound

\[
 D_H\le B_H\le\frac{w}{\eta}\operatorname{osc}(f)\le\frac{w}{(1-u+l)^w}. \tag{7}
\]

It is typically very pessimistic. It makes the bridge fully quantitative without assuming rapid mixing, but should not be used to suggest a practical mixing time or practical response tolerance at w=2880.

More generally, a measured or proved oscillation-contraction sequence c_r with osc(P^r f)<=c_r osc(f) gives B_H<=osc(f)sum c_r. Directly bounding D_H can be much sharper than bounding the full span.

For an actual nonstationary process, a law-level perturbation expansion also gives

\[
 \|\mathcal L(X_t)-\pi\|_{TV}
 \le (1-\eta)^{\lfloor t/w\rfloor}
 +\sum_{s=0}^{t-1}(1-\eta)^{\lfloor(t-1-s)/w\rfloor}\mathbb E|e_s|. \tag{8}
\]

Here total variation is sup_A|mu(A)−nu(A)|. To verify (8), write nu_{s+1}=nu_s P+zeta_s, where zeta_s has total mass zero and TV norm at most E|e_s|, iterate this equality, and apply the reference contraction to each signed measure. Under a uniform epsilon bound the second term is at most epsilon w/eta. This offers pointwise-in-time distribution control, but the Poisson reward bound is generally more informative.

## 6. What is sufficient to transfer O(1/w), and what is not proved

For fixed strict-interior l<alpha<u, the reference mean m_w is O(1/w). Therefore the following are rigorous sufficient conditions:

* In stationarity, D_{H,w} E|e_t|=O(1/w) transfers stationary MAE O(1/w) to the actual full-history process.
* For a nonstationary long-run average, D_{H,w} limsup_N N^(-1)sum |e_t|=O(1/w) transfers the same long-run-average rate via (4).
* For finite experiments, retain the additional B_{H,w}/N and martingale terms in (3); their scaling cannot be dropped.

Using only (7), a fully explicit but severe sufficient tolerance is epsilon_w=O((1−u+l)^w/w²). If a separate proof or certified computation gives D_{H,w}<=C independent of w, then the much more natural epsilon_w=O(1/w) is sufficient. **A uniform-in-w bound for D_H is not proved here.** Small-state numerical evidence below must not be promoted to that theorem.

The coefficient D_H cannot vanish as O(1/w) in the strict-interior regime. Indeed, for 1<=k<=w−1 the all-zero and all-one reference Poisson equations give respectively

\[
 H(0,\ldots,0,1)-H(0,\ldots,0)
 =-\frac{\alpha-m_w}{u},
\]

\[
 H(1,\ldots,1)-H(1,\ldots,1,0)
 =\frac{1-\alpha-m_w}{1-l}.
\]

Consequently,

\[
 D_{H,w}\ge
 \max\left\{\frac{|\alpha-m_w|}{u},\frac{|1-\alpha-m_w|}{1-l}\right\}, \tag{9}
\]

whose limit is positive when m_w=O(1/w). Thus this generic absolute-error certificate cannot establish O(1/w) from a fixed nonzero epsilon merely by hoping that D_H shrinks with w.

This is a limitation of the sufficient bound, not a counterexample showing that fixed epsilon must destroy the actual rate. Indeed, the earlier count-only-response theorem already shows that some fixed perturbations preserve O(1/w). No general proof or counterexample for *all* full-history perturbations of a fixed positive size has been established in this exploration. Likewise, the exact exchangeable stationary distribution must not be reused under arbitrary hidden-state dependence.

## 7. Numerical Poisson probes — evidence only

The companion script `probe_poisson.py` constructs the full finite reference chain and solves the Poisson equation, anchoring H at a state of maximal per-word invariant mass to avoid numerical ill-conditioning from pinning an exponentially rare state. Run with `python -s` in this environment to avoid the unrelated user-site NumPy/SciPy version mismatch.

Representative results:

| w | alpha | l | u | D_H | span(H) | Poisson residual |
|---:|---:|---:|---:|---:|---:|---:|
| 10 | .3 | .1 | .6 | .7347550731 | 3.1303840233 | 1.9e−15 |
| 12 | .3 | .1 | .6 | .7205878215 | 3.4682962385 | 4.4e−14 |
| 10 | .5 | .1 | .9 | .5587117354 | 2.0258932943 | 3.1e−15 |
| 12 | .5 | .1 | .9 | .5682994179 | 2.4501341440 | 1.5e−14 |
| 10 | .1 | .01 | .3 | .8868927484 | 4.5 | 3.5e−15 |

These suggest D_H is far smaller than the generic Doeblin bound, but the state space grows as 2^w. Direct enumeration is unsuitable for the paper's w=2880. Establishing a structural analytical bound, or a certifiable low-dimensional Poisson approximation, remains necessary for a practical large-w coefficient.

## 8. A residual certificate avoids claiming an exact computed Poisson solution

For any explicit trial potential V, scalar m_tilde, and certified residual bound

\[
 \left|f(x)-\widetilde m-(V-PV)(x)\right|\le r_0
 \quad\text{for every binary window }x,
\]

define B_V=osc(V), D_V=max_x|V(T_1x)−V(T_0x)|. The same derivation gives

\[
 \left|\frac1N\sum_{t=0}^{N-1}\mathbb E f(X_t)-\widetilde m\right|
 \le r_0+\frac{B_V}{N}+D_V\frac1N\sum_{t=0}^{N-1}\mathbb E|e_t|. \tag{10}
\]

The high-probability version adds D_V sqrt(log(1/delta)/(2N)) on the upper side. Under an actual stationary marginal, the boundary term vanishes. Thus a structured approximate potential plus a rigorous residual bound suffices; numerical approximation of a Poisson solution need not be silently treated as exact.

## 9. How this connects to the MPC without circularity

The bridge separates two independently checkable mathematical statements:

1. The known reference window policy has mean error m_w and a Poisson potential or residual certificate.
2. A property of the actual MPC solution, forecast error distribution, and applied relaxation parameters controls the *conditional probability* mismatch E|p_{t+1}−q_{S_t}| or its time average.

The first statement is supplied here. The second must be proved from the actual solver/dynamics or established through an explicit model assumption with observable certificates; it cannot be inferred from the sign of the multiplicative h update. Importantly, that second statement may allow arbitrary dependence on the full MPC state and the entire history: count-only sufficiency is no longer required for Theorem 1.
