# Full MPC to window-feedback theory: independent audit

Date: 2026-09-08. Scope: the manuscript formula and mathematical MPC model only. Existing MATLAB variants are excluded. This file records exact obstructions, candidate-bridge verification, and the assumptions necessary for valid conclusions; it does not modify the manuscript.

## 1. Audit standard for a general connection theorem

A connection must identify the complete observed history $\mathcal F_t$, the selected applied control, the nominal one-step state, and the actual violation event. It must then derive a bound on the **actual** full-history conditional probability $p_{t+1}=\Pr(O_{t+1}=1\mid\mathcal F_t)$ from explicit model, uncertainty, and optimizer conditions. Marginal disturbance laws, expanded feasible sets, and a visually monotone cost curve do not individually establish such a bound.

Two mathematically distinct connections are possible. For the original multiplicative update, uniform response bounds in the two log-amplitude tails are enough for the already proved exponential-moment and cumulative-risk results; no retained-count sufficient statistic is needed. For the exact stationary window law, one must instead prove $p_{t+1}=q_{S_t}$ or quantify an approximation to that law. The proofs and conclusions for these two routes must remain distinct.

## 2. Unique convex MPC optimum can move risk opposite to the relaxation

Let a nonnegative common horizon relaxation $r=-h$ apply to both predicted states. Consider the LP

$$\min_{x,y}\;3x+y\quad\text{subject to }0\le x,y\le2,\quad x+y\ge2,\quad x\le1+r,\quad y\le1+r.$$

For every $r\in[0,1]$ the feasible set is nonempty and expands with $r$. The unique optimizer is

$$x^*(r)=1-r,\qquad y^*(r)=1+r,$$

with value $4-2r$.

**Proof.** Positive objective coefficients force $x+y=2$ at an optimum. Substitution gives objective $2+2x$, minimized by the smallest feasible $x$. The bound $y\le1+r$ imposes $x\ge1-r$, and this endpoint is feasible. Strict increase of the reduced objective proves uniqueness.

Let the applied first-step realized state be $x^*(r)+\varepsilon$ with nominal safety limit $1$ and $\varepsilon\sim N(0,\sigma^2)$ independent of the past. Then

$$p(r)=\Pr(x^*(r)+\varepsilon>1)=1-\Phi(r/\sigma),$$

which **strictly decreases** with the relaxation over $[0,1]$. Thus convexity, unique optimizers, nonnegative linear economic costs, nested feasible sets, and a shared horizon relaxation do not imply the intended first-step risk direction. The objective value is monotone under feasible-set inclusion; the selected first-step residual need not be.

This is a counterexample to an unconditional structural implication, not a claim that this exact two-variable instance is the user's data-center model.

## 3. Relaxation-only updates may have an irreducible nominal risk floor

Fix $U>0$, let $r=-h>0$, and solve the scalar LP

$$\min_u(U-u)\quad\text{subject to }0\le u\le U,\quad u\le r.$$

The unique optimizer is $u^*(r)=\min(r,U)$. Take the nominal safety limit as zero and the realized state as $u^*(r)+\varepsilon$, with independent $N(0,\sigma^2)$ errors. Then

$$p(r)=\Phi(\min(r,U)/\sigma)\ge1/2.$$

The constrained nominal model is feasible for every $r\ge0$, including the nominal endpoint $r=0$. Nevertheless, the allowed sign $h\le0$ provides no ability to move the nominal plan below the nominal safety boundary; its limiting violation probability is $1/2$.

### 3.1 The original update provably fails to calibrate in this example

Choose $w\ge2$, $1/(2w)<\alpha<1/2$, and a gain ensuring positive update factors. Use the established notation $n=w-1$, $c=n/w$, $a=(w\alpha-1/2)/(w-1)$, and $x_t=\log r_t$. The exact finite-time update identity gives

$$\frac{x_N-x_0}{N}=\frac c\chi(a-\bar O_N)-\frac{D_N}{\chi wN}-\bar r_N,$$

where $|D_N|$ is bounded independently of $N$ and $\bar r_N\ge0$ is the average logarithmic-curvature remainder. Since all actual conditional probabilities are at least $1/2$, the bounded martingale-difference strong law gives $\liminf_N\bar O_N\ge1/2$ almost surely. Hence

$$\limsup_N\frac{x_N}{N}\le\frac c\chi(a-1/2)<0\quad\text{almost surely}.$$

Thus $r_N$ tends to zero at least exponentially along almost every trajectory. The response formula now gives $p_{N+1}\to1/2$. Cesàro averaging and the same martingale strong law imply

$$\bar O_N\longrightarrow1/2,\qquad\frac1N\sum_{t=1}^{N}p_t\longrightarrow1/2\quad\text{almost surely},$$

despite the target $\alpha<1/2$. This is a complete failure mechanism with a unique convex optimizer, smooth iid errors, and a globally monotone risk response. It rules out an unconditional calibration theorem for all MPCs having the paper's relaxation structure.

## 4. Constraint inactivity can destroy upper-tail recovery

Let the nominal first-step residual be $u-M$ relative to its safety threshold, where $M>0$. Consider the unique LP optimizer

$$\min_u u\quad\text{subject to }0\le u\le U,\quad u-M\le r.$$

For every $r\ge0$, the optimizer is $u=0$; the relaxed safety bound never controls that choice. With iid Gaussian error the violation probability is the constant $p_s=1-\Phi(M/\sigma)$. Choose $M$ so that $0<p_s<p_\dagger$, where

$$\Psi(p)=\mathbb E f(\operatorname{Bin}(w-1,p)),\qquad\Psi(p_\dagger)=0.$$

The violation bits are now iid Bernoulli($p_s$), and the finite-memory sequence of log increments is stationary and ergodic after warmup. Its strong law gives

$$\frac{x_N-x_0}{N}\longrightarrow\Psi(p_s)>0\quad\text{almost surely}.$$

Consequently $r_N$ diverges exponentially, although the actual risk remains safely below the target. This is failure of bounded relaxation amplitude, not instability of the physical state. It shows why an upper-tail bridge must prove a lower bound on the selected first-step violation probability; mere availability of less safe actions is insufficient.

## 5. Retained count is generally not a sufficient statistic for the implemented update

Even a strictly monotone scalar risk response does not make the original multiplicative controller equivalent to a fixed map $q_{S_t}$. For $w=2$, the retained count is simply the latest observation. With the same initial log amplitude $x_0$, consider the two histories $(O_1,O_2)=(0,0)$ and $(1,0)$. Both yield $S_2=0$, but

$$x_2^{00}=x_0+2f_0,\qquad x_2^{10}=x_0+f_1+f_0,$$

and $f_0>f_1$. Whenever the risk response is strictly monotone on this range, the two next-step probabilities differ. In the Gaussian scalar LP of Section 3, choose $U$ large enough that both amplitudes lie below its saturation level; both histories then have strictly positive probability and the inequality is strict.

Therefore an exact retained-count Markov law cannot be inferred from the form of the feedback statistic. Earlier observations remain encoded in the accumulated multiplicative amplitude. Varying the initial amplitude while holding the memory fixed provides an even simpler obstruction to a uniform count-only approximation over arbitrary initializations.

## 6. Exact residual representation and optimizer-face audit criteria

Let $\widehat s_{t+1}$ denote the planned first-step state under the **selected applied** control, and define its nominal signed residual $d_t=G\widehat s_{t+1}-g$. Set $e_{t+1}=G(s_{t+1}^{\rm real}-\widehat s_{t+1})$. Then, identically,

$$O_{t+1}=\mathbf1\{d_t+e_{t+1}>0\},\qquad p_{t+1}=1-F_t(-d_t),$$

where $F_t(z)=\Pr(e_{t+1}\le z\mid\mathcal F_t)$. No independence assumption is needed for this identity, but the conditional error law can depend on the chosen action, state, and entire history. Any post-processing of the executed control must be included in the state residual or in this error term.

If certified bounds $d_-\le d_t\le d_+$ and conditional CDF envelopes $F_-\le F_t\le F_+$ hold uniformly over the relevant histories and selected controls, then

$$1-F_+(-d_-)\le p_{t+1}\le1-F_-(-d_+).$$

For a nonunique MPC optimum, bounds on the whole optimal face protect against unspecified solver tie-breaking. If an LP is written as $\min c^\top z$ over a feasible polyhedron with attained finite optimum $J^*$, the extrema of an affine nominal residual over

$$\{z:\ z\text{ feasible},\ c^\top z=J^*\}$$

bound every optimal selection. These are additional optimization certificates. To obtain a uniform tail theorem, the face-extremum and error-envelope conditions must hold over **all** relevant full histories, other adaptive coordinates, and tail amplitudes; checking isolated parameter samples is not a proof of that uniform statement.

Quantile re-expressions must handle atoms. The exact CDF inequalities above are safe. Replacing them mechanically by inequalities involving a generalized inverse CDF can reverse or lose a boundary inequality when the distribution has jumps; continuity and the needed quantile equality, or explicit left/right quantile definitions, must be stated.

## 7. Coupled-constraint and uniformity limitations

For the ESS lower and upper limits, the realized violation events are disjoint when $E_{\min}<E_{\max}$, so their actual probabilities satisfy $p_1+p_2\le1$. Independent marginal attainability does not imply simultaneous independent endpoint selection. Any componentwise tail condition must be checked in the presence of the other components' relaxation values and the shared physical state. For example, two simultaneous lower bounds $p_i\ge p_{+,i}$ in a jointly reachable high-relaxation region are impossible if $p_{+,1}+p_{+,2}>1$.

The bounds used in the previous full-history tail theorem are uniform conditional implications, not average empirical correlations. A fitted positive slope of violation rate versus relaxation, or a collection of finitely many successful sampled histories, does not by itself prove them. A valid mathematical bridge may present them as explicit conditional model certificates and explain which pieces follow from LP structure, error assumptions, and a specified state/history domain.

If optimizer and uncertainty certificates are established only on an operating domain, all-time conclusions additionally require the actual histories to remain in that domain almost surely, or a separately proved invariant-domain/recursive-feasibility property. Without that premise, the certified statements apply only until exit or on an explicitly specified no-exit event. A finite collection of sampled operating points does not establish domain invariance.

## 8. Independent audit of the full-history Poisson bridge

The complete candidate `perturbation_bridge.md` was read independently. **Verdict: passes** on all substantive statements, including its stationary and nonstationary distinctions.

For the finite irreducible aperiodic reference window chain, let $P$ be its kernel, $f(x)=||x|/w-\alpha|$, $m=\pi f$, and solve $H-PH=f-m$. Define

$$B_H=\operatorname{osc}(H),\quad D_H=\max_x|H(T_1x)-H(T_0x)|,\quad e_t=p_{t+1}-q_{S_t}.$$

Conditional expectation under the actual process is $\mathbb E[H(X_{t+1})\mid\mathcal F_t]=PH(X_t)+e_t[H(T_1X_t)-H(T_0X_t)]$. Consequently the exact decomposition is

$$\sum_{t=0}^{N-1}(f(X_t)-m)=H(X_0)-H(X_N)+\sum_{t=0}^{N-1}e_t\Delta H(X_t)+\sum_{t=0}^{N-1}M_{t+1},$$

where $M_{t+1}=H(X_{t+1})-\mathbb E[H(X_{t+1})\mid\mathcal F_t]$. Every sign in the candidate matches this derivation. The expected boundary magnitude is at most $B_H$, while each conditional martingale range has length at most $D_H$. Thus its expected average-reward bound, one-sided confidence term $D_H\sqrt{\log(1/\delta)/(2N)}$, and almost-sure long-run bound are correct without any Markov or stationary assumption on the actual process. Stationarity of only the window marginal suffices for the corresponding boundary cancellation in expectation.

### 8.1 Minorization, transient total variation, and sensitivity constants

For $q_s\in\{\ell,u\}$, generating a particular word $z$ over $w$ steps has probability at least $\ell^{|z|}(1-u)^{w-|z|}$. Summing over words gives the valid common minorization mass

$$\eta=(1-u+\ell)^w.$$

Hence the reference oscillation contracts by $1-\eta$ per block. Summing the Poisson series proves $D_H\le B_H\le w\operatorname{osc}(f)/\eta$. The candidate's actual-law expansion $\nu_{s+1}=\nu_sP+\zeta_s$, with zero-mass defect and $\|\zeta_s\|_{TV}\le\mathbb E|e_s|$, is correct. Iteration gives its transient convolution inequality, with exponent $\lfloor(t-1-s)/w\rfloor$ for the defect inserted at step $s$. No factor of two is missing under the stated convention $\|\mu-\nu\|_{TV}=\sup_A|\mu(A)-\nu(A)|$.

The all-zero and all-one self-loop Poisson equations also give the exact lower bound

$$D_H\ge\max\left\{\frac{|\alpha-m|}{u},\frac{|1-\alpha-m|}{1-\ell}\right\}$$

whenever the threshold index is interior. Thus $D_H$ remains bounded below by a positive constant when $m=O(1/w)$. The candidate correctly states that preserving an $O(1/w)$ reward bound through this generic absolute-error certificate requires $D_H\epsilon_w=O(1/w)$. Merely fixed small probability error is insufficient for **that certificate**. This is not a proof that every fixed perturbation destroys the true feedback rate, and the candidate correctly avoids such a claim.

Its residual-potential extension is also correct: a uniform residual $|f-\widetilde m-(V-PV)|\le r_0$ contributes at most $r_0$ to the averaged bound, and the same derivation uses $B_V,D_V$. A numerical Poisson solution therefore need not be silently treated as exact if its residual can be certified over the entire state space.

### 8.2 Conditional CDF and nominal-margin tube to probability mismatch

The proposed tube connection is valid. Let the continuous reference CDF $F_0$ have a globally bounded density, hence $|F_0(x)-F_0(y)|\le L|x-y|$. Choose nominal reference margins $m_s$ satisfying $1-F_0(-m_s)=q_s$. If, for the actual selected control and full history,

$$\sup_z|F_t(z)-F_0(z)|\le\delta_F,\qquad |d_t-m_{S_t}|\le\delta_m,$$

then the exact residual representation yields

$$|p_{t+1}-q_{S_t}|\le\delta_F+L\delta_m.$$

**Proof.** Insert and subtract $F_0(-d_t)$ in $|F_t(-d_t)-F_0(-m_{S_t})|$, then apply the two assumed bounds. The actual conditional CDF may have atoms; continuity is needed only for the stated reference construction and its Lipschitz bound. This connection still needs a uniform or suitable average certificate for the selected-margin tube and conditional error CDF. Neither follows merely from the multiplicative update or an unconditional error histogram.

## 9. Independent full audit of the optimizer/CDF response certificate

The full candidate `mpc_response_certificate.md` was subsequently read. **Verdict: mathematical substance passes.** Three small precision edits were sent to its author: specify admissible initializations satisfying the domain premises; use suprema/infima or explicitly require finite attained secondary residual extrema when the primary optimal face need not be compact; and, for a fixed lower physical bound over a varying $d$ interval, spell out $L\le-d_{\max}$ as a sufficient uniform-feasibility condition.

### 9.1 Main bridge and Gaussian specialization

The low-tail nominal certificate $m\le\ell$ implies $F_t(-m)\ge F_t(-\ell)\ge\underline F(-\ell)$ and hence $p\le1-\underline F(-\ell)$. The high-tail nominal certificate $m\ge u$ gives the reversed inequalities and $p\ge1-\overline F(-u)$. Only the true conditional CDF's monotonicity is used; envelope functions themselves need not be continuous or monotone. Strict opposite signs of the explicit binomial log-drift function at these two certified rates place them on opposite sides of $p_\dagger$, proving the full-history recovery assumption. The reference-amplitude scale in $x=\log(r/r_{\rm ref})$ makes the logarithm dimensionless and leaves its update increments unchanged. The displayed conversion from $r_L,r_H$ to a common log threshold is correct.

For conditionally Gaussian error with mean bias in $[b_-,b_+]$ and known standard deviation $\sigma$, the envelopes have the right orientation: lower CDF uses $b_+$ and upper CDF uses $b_-$. The corresponding margin requirements are $\ell+b_+\le\sigma\Phi^{-1}(q_L)$ and $u+b_-\ge\sigma\Phi^{-1}(q_H)$. This specialization is correct and is not imposed on the general theorem.

### 9.2 Secondary LPs, dual multipliers, and objective tolerance

The original optimal face is the feasible polyhedron intersected with $c^\top v=J^*$. Extrema of the nominal affine residual on that face therefore certify every optimizer selection. If $a=A^\top\lambda+E^\top\nu+\eta c$ with $\lambda\ge0$, then every feasible optimum obeys

$$a^\top v+\beta\le\beta+\lambda^\top b+\nu^\top d+\eta J^*.$$

Applying the same identity to $-a$ gives the stated lower certificate. Under a feasible objective gap in $[0,\tau]$, the additional upper error is $\max(\eta,0)\tau$, and the lower certificate loses that same nonnegative amount. The candidate's signs and tolerance qualification are correct; primal feasibility errors need their own multiplier-weighted terms.

For the restricted low-residual problem, the dual stationarity equation $c+A^\top\lambda+\rho a+E^\top\nu=0$, with $\lambda,\rho\ge0$, gives lower bound $-\lambda^\top b-\rho(u-\beta)-\nu^\top d$. A feasible original plan whose cost is strictly below this lower bound excludes every original optimizer with residual at most $u$. This is a valid noncircular economic-preference certificate.

### 9.3 Infinite-tail saturation and continuous-parameter coverage

If all target horizon rows are uniformly bounded over the domain after removing those rows, a relaxation exceeding that bound makes them redundant. The optimum face is then independent of that relaxation, provided the remaining data are held in the stated parameterization. Certifying this saturated problem covers the otherwise unbounded high-relaxation tail. The operating-domain bound used for this reduction is explicitly an external premise and does not itself prove state boundedness.

For fixed constraint matrix after equality elimination, affine right-hand side, affine objective coefficients, and affine residual with fixed decision coefficient, each invertible active basis gives an affine vertex and affine multipliers. Primal feasibility and nonnegative dual multipliers define a polyhedral critical region. The claim remains correct even though the primary optimal value may be quadratic in the parameters when both right-hand side and objective vary: the relevant residual and feasibility/multiplier tests remain affine.

Completeness under degeneracy is also valid. At an optimal vertex, $-c$ belongs to the conic hull of its active normals. A conic Carathéodory reduction selects linearly independent generators with nonnegative coefficients; these can be supplemented by further independent active normals with zero coefficients until they form a full basis. The resulting basis represents that vertex and optimal multipliers. Since a linear residual achieves its extrema at vertices of a compact optimal face, enumeration covers every required extreme. Each parameter optimization is an LP over a compact polyhedral critical region. This finite procedure can be expensive, but the proof does not replace it by random sampling or unpartitioned parameter-vertex checks.

### 9.4 Constructive shared-horizon model

The proposed example $\min_m(J_0-cm)$, $L\le m\le M$, $m\le r$, $m+d\le r$, with $c,d>0$ and $L\le-d,L\le M$, has the unique optimizer

$$m^*(r)=\min(M,r-d).$$

The future-step constraint is the stricter one, giving a negative first-step margin when $r$ is small. Its low-tail upper bound $r_L-d_{\min}$ and high-tail lower bound $\min(M_{\min},r_H-d_{\max})$ are correct. Together with an explicitly specified conditional error CDF they yield actual optimizer-based recovery, without assuming that recovery at the outset. This supplies a positive constructive bridge alongside the failure examples in Sections 2–4. It is carefully labelled a constructed model and is not claimed to certify the user's full data-center MPC instance.

## 10. Audit outcome and remaining instance-level obligations

Both mathematical connection routes pass independent audit: optimizer residual/CDF certificates imply the original update's full-history tail-response guarantees; nominal-margin/CDF tubes combined with a Poisson or residual-potential certificate transfer reference window-error bounds with an explicit sensitivity penalty.

The remaining obligations concern the actual paper instance: a feasible invariant operating domain or an explicit domain-stay premise, uniform conditional error envelopes, residual certificates over all relevant optimizer selections and parameter regions, and a usable large-window sensitivity/residual certificate if transferring the $O(1/w)$ window MAE result. The counterexamples prove these obligations cannot be deleted by appealing solely to convex MPC, nested relaxed constraints, a common horizon relaxation, or monotone economic value. No unconditional theorem for the full manuscript MPC has been claimed or verified.

**Final precision check.** The response-certificate author implemented the requested edits: the main tail conditions now use supremum/infimum, the secondary LP statement requires finite residual extrema, the arbitrary-initialization conclusion is explicitly conditional on admissibility and domain/feasibility premises, and the varying-$d$ example includes $L\le-d_{\max}$ and $L\le M_{\min}$. These edits were reread and accepted. No substantive mathematical defect remains unresolved in either connection draft. Supplemental rerunning of the Poisson probe with isolated user-site imports completed all 27 cases for $w=2,\ldots,10$ with maximum displayed Poisson residual below $3.45\times10^{-15}$; this is a numerical consistency check, not a proof of a sensitivity bound uniform in $w$.

## 11. Bounded final audit of the integrated report

The root report `MPC与概率理论的一般性连接.md` was read in full after integration, and its revised canonical-example certification was reread. **Final verdict: passes.**

* Theorem A uses the exact strict-violation CDF complement, correct margin signs, and explicitly nondecreasing conditional CDF envelopes. Including the selected action in the filtration makes the nominal margin measurable; post-processing error remains included in the actual residual.
* Theorem B retains the interior target and positive-factor requirements, all-time validity of the MPC certificates, admissible finite initialization, and the certified-domain/continued-solvability premise. Its block drift has the correct $n-1$ warmup steps, log-amplitude increment timing, drift sign, and finite-history correction. The conclusion concerns parameter moments and cumulative empirical/conditional risk, not the full physical state or every moving window.
* The scalar two-step example is feasible for every positive relaxation, has the stated unique optimizer, and truly produces a low-tail negative nominal margin through its future constraint. The revised proof does not depend on the decimal root estimate: Mills inequalities imply $p_L<0.05$ and $p_H>0.1>a_w>p_\dagger$. For $p<0.05$, $\mathbb Eb>319/6400=0.04984375>0.0498$ and $|b|<0.9$, so $\Psi(p)>0.00498-0.81/182>0$. These strict inequalities certify the crossing.
* Theorem C uses current-window reward $g_w(X_t)$, its matching current retained count $S_t$, and next-event conditional probability $p_{t+1}$. Its Poisson telescoping, martingale sign, boundary indices, stationary-marginal specialization, and time-average interpretation are aligned correctly. The probability-tube transfer and the stated additional sensitivity requirement preserve the distinction between a conditional connection theorem and an instance-level verification.

A small self-contained presentation clarification was suggested: explicitly recall the reference endpoint selector and $0<\ell<u<1$ in Section 6, with $\ell<\alpha<u$ for the asymptotic $O(1/w)$ claim. Its mathematical referent is already the previously established reference policy; no substantive proof error was found. This final integration check closes the requested audit. No further mathematical exploration is included.
