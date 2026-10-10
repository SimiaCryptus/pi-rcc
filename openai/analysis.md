# π from Two Directions: The Simplest Increment vs. the Irrationality Exponent

This note compares two artifacts that both target **π**, from two different but related traditions:

- `README.md` — *The Simplest Increment*: analytic dynamics and numerical analysis (the map `x → x + sin(x)`).
- `lean/ComparatorChallenges/PiExponent.lean` — Diophantine approximation: the claim that the irrationality exponent of π equals 2.

---

## 1. Summary of Each Artifact

### The Simplest Increment (`README.md`)

- **Core object:** the map $x \mapsto x + \sin(x)$.
- **Mechanism:** the map relies on three facts about sine at π: $\sin(\pi)=0$, $\sin'(\pi)=-1$ and $\sin''(\pi)=0$. Writing $e = x - \pi$, the error evolves as
  $$e_{n+1} = e_n + \sin(\pi + e_n) = e_n - \sin(e_n) = \frac{e_n^3}{6} - \frac{e_n^5}{120} + \dots$$
- **Domain:** local Taylor analysis, superattracting fixed points (multiplier exactly 0) and constructive approximation.
- **Status:** elementary and fully understood. The companion harness verifies it symbolically and in exact arithmetic.

### The Irrationality Exponent (`PiExponent.lean`)

- **Core object:** the irrationality measure $\mu(\pi)$.
- **Statement:** theorem `OAI.PiExponent.main` asserts two conjoined facts.
  1. For every $\nu > 2$ there is a threshold $Q \ge 2$ such that, for all $p \in \mathbb{Z}$ and all $q \ge Q$:
     $$\left|\pi - \tfrac{p}{q}\right| \ge q^{-\nu}.$$
  2. Consider the exponents $\nu > 0$ for which infinitely many rationals $r$ with $\mathrm{den}(r) \ge 2$ satisfy $0 < |\pi - r| < \mathrm{den}(r)^{-\nu}$. The supremum of these exponents is exactly $2$.
- **How the two parts relate:** part 1 alone gives $\mu(\pi) \le 2$. Part 2 then follows from part 1 together with Dirichlet's theorem, which gives $\mu \ge 2$ for every irrational.
- **Domain:** metric Diophantine approximation and transcendence theory.
- **Status:** this is **a famous open problem**.
  - The proof is `sorry`.
  - The comparator config (`PiExponent.json`) permits only the standard axioms `propext`, `Quot.sound` and `Classical.choice`.
  - The best proven upper bound is $\mu(\pi) \le 7.103\ldots$ (Zeilberger–Zudilin, 2020).

---

## 2. Conceptual Comparison

| Dimension | The Simplest Increment | Irrationality Exponent |
| :--- | :--- | :--- |
| **Perspective** | Constructive and generative: how fast can a value be pushed toward π? | Structural and obstructive: how tightly can fractions cluster around π? |
| **Approximants** | Reals produced by evaluating a transcendental function (sin) | Rationals $p/q$, measured against their denominator |
| **Rate** | Cubic in iterations: $\lvert e_n\rvert \approx C^{3^n}$, so about $\log_3\log(1/\varepsilon)$ steps | Power law in the denominator: $\lvert\pi - p/q\rvert \approx q^{-\mu}$ |
| **Truth status** | Follows from Taylor series and the symmetry of sine about π | Open conjecture at the frontier of number theory |
| **Tooling** | Computer algebra, exact rationals, slope fitting | Padé and hypergeometric constructions, arithmetic integrals, Lean 4 |

---

## 3. Connections

### A. Dynamic approximation vs. rational representation

- The map leaves the algebraic numbers immediately.
  - Starting from $x_0 = 3$, the first iterate is $x_1 = 3 + \sin 3$.
  - $\sin 3$ is transcendental by Lindemann–Weierstrass, since $\sin a$ is transcendental for every nonzero algebraic $a$.
- Exact rational certificates therefore require truncating sine, for example with Taylor polynomials.
- Rational addresses therefore require truncating sine, for example with Taylor polynomials. (Earlier drafts called these "certificates". They are not verifiable on their own; checking one means recomputing π.)
- The companion report places the length of these addresses in the **optimal logarithmic regime**: address length grows like $O(\log(1/\varepsilon))$. This measures address length, not closeness to the Diophantine optimum.
   - A generic truncated address prefix $p/q$ achieves $|\pi - p/q| \approx q^{-c}$ with $c$ close to 1.
  - Only continued-fraction convergents reach exponent 2, and part 1 of the theorem asserts that nothing does essentially better.
- So the two quantities answer different questions:
  - RCC asks *how many bits per digit*, and a constant factor is allowed.
  - $\mu(\pi)$ asks *how good the best possible fractions can be*.
- A cubic engine can be bit-optimal up to a constant factor without saying anything about $\mu(\pi)$.

### B. Where μ(π) = 2 sits on the Extension Ladder

The Extension Ladder (ℤ → ℚ → algebraic numbers → transcendentals like π → ℂ) gives the context:

- **Dirichlet:** $\mu(x) \ge 2$ for every irrational $x$.
- **Roth (1955):** $\mu(\alpha) = 2$ for every algebraic irrational $\alpha$.
- **Khintchine:** $\mu(x) = 2$ for almost every real $x$.
- **Liouville numbers:** these transcendentals have $\mu = \infty$.

The Lean theorem asserts that π, despite sitting above the algebraic numbers, has the same Diophantine rigidity as an algebraic number or a generic real.

### C. Local symmetry vs. global arithmetic

- **Cubic convergence is purely local.** It depends only on the conditions $g(\pi)=0$, $g'(\pi)=-1$ and $g''(\pi)=0$. Any $g$ with this Taylor profile converges cubically. The README's perturbed-sine counterexample keeps the first two conditions, breaks the third, and drops to quadratic convergence.
- **Bounding $\mu(\pi)$ is global and arithmetic.** Known bounds come from real integrals such as
  $$\int_0^1 \frac{x^n(1-x)^n}{1+x^2}\,dx,$$
  combined with control of their denominators across all primes, using the Prime Number Theorem. The RCC framework leans on the same theorem.

---

## 4. Takeaway

- The Simplest Increment shows how easily smooth calculus locks onto π: three Taylor conditions produce cubic convergence.
- `PiExponent.lean` marks how hard it is to understand π relative to the integers. Despite work by Hata, Salikhov and Zeilberger–Zudilin, proving that $\mu(\pi) = 2$ remains open.
- Together they show both sides of π: it is analytically accessible through the symmetries of the continuum, yet arithmetically elusive from the vantage point of fractions.