# Independent audit: actual multiplicative update, Theorems 2–5

Auditor: window-theory agent. Date: 2026-09-08.

Scope: independently re-derived Theorems 2–5 of `actual_update.md`, including the martingale constant, stationary bias bounds, discrete stationary envelope, and w=2 formula. The stationary-existence theorem was deliberately excluded and is assigned to another auditor. No manuscript or MATLAB code was used or modified.

## Verdict

**Theorems 2–5 pass substantive mathematical audit under their stated hypotheses.** Two corrections in the read version are needed: equation (8) must include a plus sign before `(w−2)/(2N)`; Corollary 2b says “three distinct terms” but displays and describes four. The former was already identified by the root agent and assigned to the author for correction.

The principal logical restriction remains essential: stationarity and integrable log amplitude are hypotheses of Theorems 4–5, not consequences of the identities. Theorems 2–3 apply directly to arbitrary binary histories satisfying the positive-factor recurrence.

## Re-derivation of the exact identity

Write n=w−1, c=n/w, a=A/c, and D=sum S_t−n sum O_t. Then directly

\[
 \sum_{t=1}^N b_t=cN(a-\bar O_N)-D/w.
\]

Using log(1+u)=u−r(u),

\[
 \chi(x_N-x_0)=cN(a-\bar O_N)-D/w-\chi N R_N.
\]

Solving gives exactly

\[
 \bar O_N=a-\frac{\chi(x_N-x_0)}{cN}-\frac\chi c R_N-\frac D{nN}.
\]

All signs and factors in (3) are correct. The boundary identity is a sum of n−1 shifted interval differences, the j-th bounded by min(j,N)<=j. Therefore |D|<=n(n−1)/2 also covers N smaller than the memory length. The liminf condition on normalized log-amplitude drift gives the stated limsup bound after dropping R_N>=0.

## Martingale probability constant

Conditionally on the past, p_t−O_t has mean zero and lies in [p_t−1,p_t], an interval of length exactly one. Conditional Hoeffding gives the moment-generating bound exp(lambda²/8) for each increment, so

\[
 \Pr(\bar p_N-\bar O_N>z)\le e^{-2Nz^2}.
\]

Thus sqrt(log(1/delta)/(2N)) is the correct one-sided deviation term. No independence of observations is required. Combining it with the exact identity gives equation (8) with the missing **plus** sign restored. Its fixed-N interpretation, the event-conditioned endpoint bound, and the union-bound loss delta+eta are correctly stated. It must not be silently used at an arbitrary data-dependent stopping time.

## Stationary bias and magnitude

Strict stationarity and E|x_t|<infinity imply E(x_t−x_{t−1})=0. Also E S_t=n mu. Hence

\[
 0=\frac c\chi(a-\mu)-E r(b_t/\chi),
\]

which is exactly (10). Since r>=0 with equality only at zero, the claimed equality and strictness conditions are correct.

The exact integral representation

\[
 r(u)=u^2\int_0^1\frac{s}{1+su}\,ds
\]

and the support u in [−B/chi,A/chi] give the denominator bounds in (11) with **chi+A** in the lower bound and **chi−B** in the upper bound. These signs are correct. Given F_{t−1}, only the newest O_t remains random in b_t, so its conditional variance is p_t(1−p_t)/w². This verifies (12).

The almost-sure stationary conclusion also passes: stationarity plus integrability gives sum_N Pr(|x_N|>epsilon N)<infinity, hence x_N/N tends to zero almost surely without independence. Apply Birkhoff to the bounded remainder and the exact identity. Bounded martingale differences make the average conditional risks have the same sample-average limit.

## Sharp discrete envelope

Let Z=f(S) and H(z)=w[A−chi(e^z−1)]. Then H(f_k)=k, H is strictly concave, and E Z=0.

For the lower bound, the chord joining the two extreme support points lies below H throughout the interval. At z=0 its value is n f_0/(f_0−f_n). For the upper bound when s=w alpha−1/2 is not integral, the secant between the two support points immediately bracketing zero lies **above H at every other discrete support point**, because those points all lie outside the secant interval. Its value at zero is j+f_j/(f_j−f_{j+1}). Strict concavity puts that value strictly below H(0)=s. The integer case follows from Jensen and is attained algebraically by S=s.

The two endpoint-support constructions establish the stated sharpness under the single algebraic balance constraint. The author's warning about dynamic consistency is necessary and correct: this is not a claim that every count distribution extremizer can be realized by a stationary overlapping-window controller with integrable x.

For w=2, n=1 and only two values are available. Solving (1−mu)f_0+mu f_1=0 gives mu=f_0/(f_0−f_1), precisely equation (15). This formula is conditional on existence of the claimed stationary regime and the interior target assumptions; it does not establish that regime.

## Numerical adversarial checks

Ran 2,223 independent binary-trajectory checks over w=2,...,40, multiple interior targets and gains, and random horizons including horizons shorter than the memory. The maximum absolute residual of the exact identity was 4.440892098500626e−16; all history-bound checks passed.

For the same 2,223 parameter combinations, enumerated every feasible two-point support satisfying E f(S)=0, plus deterministic zero-increment supports. These are the vertices of the probability simplex cut by the one scalar balance equation. The smallest and largest count means matched the proposed envelope to a maximum residual of 2.220446049250313e−16. These checks corroborate the proofs rather than replace them.
