# Diophantine Approximation and Rational Certificate Complexity

> **Scope.** This note relates two ways of measuring the fractions that approach an irrational number:
>
> - **RCC** (historically "Rational Certificate Complexity", better read as *rational addressing complexity*), from `../RCC/README.md`, ranks *engines* by how fast the addresses they emit must grow.
> - **Diophantine approximation**, the setting of `lean/ComparatorChallenges/PiExponent.lean`, ranks *numbers* by how well the best possible fractions approach them.
>
> The short version: both theories ask the same question, and RCC asks it without presupposing the real line. All of Diophantine approximation happens inside RCC's best tier. The floor that RCC treats as an absolute limit (historically called "information-theoretic") is, for an individual number, *exactly* the statement that its irrationality exponent is 2. For π, that statement is the open problem.
>
> A second constraint runs through everything: **an address exists only if a finite program generates it.** A digit string with no rule behind it cannot be extended without an oracle that already holds the number. Three consequences follow (§0):
>
> - Decimal truncation is not an engine. It is a re-encoding of some other engine's output.
> - The floor bounds arithmetic *height*, not information.
> - "Almost every real" never refers to a number anyone can define.

---

## 0. Addresses, Not Certificates

Earlier drafts called each fraction an engine emits a "certificate". That word is wrong in a way that matters.

- **A certificate is cheaper to check than to produce.** A fraction is not. Nothing about $355/113$ alone tells you it is near π. Confirming it means running an engine for π far enough and comparing. That is recomputation, not verification.
- **Attaching an error bound does not fix this.** The bound is only trustworthy once it is proved, and the proof is a property of the engine, not of the fraction.

The right word is **address**.

- An engine is a finite program.
- Its rungs $p_n/q_n$ are successively longer prefixes of an infinite address.
- Each prefix locates the number within a cell of width $\varepsilon_n$.
- The address is infinite. RCC asks *how* infinite: how fast prefix length must grow as cells shrink.
- Bits are the **unit of address length**. They are not a storage cost or a proof size.

### Diophantine approximation without the real line

Diophantine approximation is usually phrased as: given a point $x \in \mathbb{R}$, how close can $p/q$ get? Every ingredient can be restated using rationals and an engine alone.

- **Error becomes tail spread.** Replace $\lvert x - p/q\rvert$ by the engine's own resolution
   $$\varepsilon_n = \sup_{m > n} \lvert p_m/q_m - p_n/q_n\rvert,$$
   or by a proved bound on it. This is computed from the address, not measured against a pre-existing point.
- **The exponent becomes a statement about cells.** "$\lvert x - p/q\rvert < q^{-\nu}$" becomes "$p/q$ lies within $q^{-\nu}$ of the engine's cell at every sufficiently fine rung."
- **Equality becomes an equivalence of addresses.** Two engines address the same number when their cells overlap at every resolution. There is no underlying point they both "hit".

Formally this is the Cauchy (or Bishop) construction. Lean's `ℝ` is also built from Cauchy sequences, although classically it admits sequences that no program generates.

That surplus does not affect `PiExponent.lean`, for two reasons:

- π is given by a program.
- The definition of $\mu$ quantifies only over rationals.

So the theorem transfers unchanged. What differs is the order of construction. The reals are derived from addresses, not assumed, and nothing in the theory refers to a number no engine addresses.

The cost of this stance shows up in statements over "all reals". The next subsection works out what survives.

### Only functionally defined addresses exist

An address must be *generated*, not transcribed. Consider a digit string with no rule behind it, such as monkeys at keyboards or a table copied from somewhere.

- It fixes finitely many rungs and then stops.
- Extending it requires an oracle that already holds the number, and that oracle is the very thing being explained.
- An infinite random string would need infinite monkeys and infinite time. It is never a completed object that a theory can refer to.

The consequences:

- **An address is the output of a finite program.** The program is the definition of the number, and the rungs are its consequences. No program, no number.
- **Addressable numbers are countable.** They are indexed by programs. There is no background stock of "arbitrary reals" waiting to be approximated.
- **Decimal truncation is not an engine.** "Take the first $n$ digits of $x$" presupposes a digit oracle for $x$.
   - What exists is a *re-encoding operator* $R$. Given an engine $E$ with a proved error bound, $R(E)$ runs $E$ to precision about $10^{-n}$ and emits a nearby fraction with denominator $10^n$.
   - Exact nearest-rounding is not computable in general, because ties cannot be decided. Any fraction within one unit of the last place suffices.
   - $R(E)$ is an engine. $R$ alone is not.
   - Digits produced *by a rule* are fine, because the rule is the engine. Liouville's $L = \sum_k 10^{-k!}$ is an example.
- **Every address has two lengths.**
   - The *program length* $K$ is fixed and finite. It is what defines the number.
   - The *prefix length* $B(\varepsilon)$ of the rational emitted at resolution $\varepsilon$ grows without bound. RCC's "address length" means this second quantity.
- **The "information-theoretic floor" is a height floor.**
   - The rung at resolution $\varepsilon$ is determined by the program together with the integer $\lceil\log_2(1/\varepsilon)\rceil$.
   - Its descriptive (Kolmogorov) complexity is therefore at most $K + O(\log\log(1/\varepsilon))$, far below $\log_2(1/\varepsilon)$.
   - So the counting bound of §3.1 does not limit information. It limits the size of the literal numerator and denominator.
   - That size is arithmetic height, precisely the quantity Diophantine approximation studies. This note calls the bound the **counting floor**.
- **Measure-theoretic "typical" no longer refers to numbers.**
   - Computable numbers form a null set, so "almost every $x$" never picks out a number anyone can define.
   - Khintchine-type statements survive only in finitary form. At each scale, the cells lying within $q^{-\nu}$ of some fraction of height $\ge Q$ have small total width. This is a statement about rationals and cells, not about any particular engine-defined number.
   - Every number this note discusses is atypical in the measure sense. Its behaviour must be proved from its own program.
- **Relative constructions are legitimate but not independent.**
   - Applying $R$, extracting convergents, or otherwise post-processing an engine $E$ yields new engines.
   - They tell us about the number $E$ defines, but they inherit $E$. They are not separate witnesses.

**Convention for the rest of this note.**

- "Address prefix" (or "rung") replaces "certificate".
- "Address length" replaces "bit cost".
- Formulas still write $\lvert x - p/q\rvert$ for readability. Read it as the tail spread defined above.

---

## 1. Two Questions About the Same Fractions

Both theories study rational numbers $p/q$ near a real $x$. They ask different questions.

| | RCC | Diophantine approximation |
| :--- | :--- | :--- |
| **Object ranked** | An engine: a procedure emitting $p_n/q_n \to x$ | A number $x$ |
| **Question** | How long an address is needed to resolve the number to scale $\varepsilon$? | How small can $\lvert x - p/q\rvert$ be relative to $q$? |
| **Quantifier** | One fixed, constructive engine | All rationals, existential |
| **Scales examined** | A full tolerance ladder $\varepsilon = 10^{-1}, 10^{-2}, \dots$ | Only the best scales (a $\limsup$) |
| **Precision of verdict** | Growth class, constant factors ignored | Exact exponent, constant factors decisive |
| **Headline invariant** | Tier: logarithmic, polynomial, worse | Irrationality exponent $\mu(x)$ |

The last row is where the two theories meet. RCC's logarithmic tier means bits $= \Theta(\log(1/\varepsilon))$. Diophantine approximation is the study of the constant hidden inside that $\Theta$.

---

## 2. A Common Vocabulary

Fix an address prefix $p/q$ in lowest terms, with resolution $\varepsilon = \lvert x - p/q\rvert > 0$ in the tail-spread sense of §0. Define three quantities.

- **Diophantine quality:** $c = \dfrac{\log(1/\varepsilon)}{\log q}$, so that $\varepsilon = q^{-c}$. This is the exponent appearing in the definition of $\mu$.
- **Address length:** $B(p/q) = \log_2\lvert p\rvert + \log_2 q$. This is RCC's measure. For fixed $x \neq 0$ we have $\lvert p\rvert \approx \lvert x\rvert q$, so $B \approx 2\log_2 q + O(1)$.
- **Address efficiency:** $\kappa = \dfrac{B}{\log_2(1/\varepsilon)}$, the number of address bits per bit of resolution. The counting floor (§0, §3.1) corresponds to $\kappa = 1$.

Asymptotically the last two are the same number in different units:
$$\kappa \approx \frac{2}{c}.$$

Note that the exact definition of "bits" does not matter here. Any choice ($\max$ instead of sum, ceilings, sign bits) changes $B$ by $O(1)$ or a fixed factor. Asymptotic exponents are unaffected, provided the choice is made once and used throughout, as RCC requires.

### Worked examples for π

| Address prefix | Error | $c$ | $B$ (bits) | $\log_2(1/\varepsilon)$ | $\kappa$ |
| :--- | :--- | :--- | :--- | :--- | :--- |
| $314159/100000$ (decimal re-encoding $R(E)$ of a π engine) | $2.65\times10^{-6}$ | 1.12 | 34.9 | 18.5 | **1.88** |
| $333/106$ (convergent) | $8.32\times10^{-5}$ | 2.01 | 15.1 | 13.6 | **1.11** |
| $22/7$ (convergent) | $1.26\times10^{-3}$ | 3.43 | 7.3 | 9.6 | **0.76** |
| $355/113$ (convergent) | $2.67\times10^{-7}$ | 3.20 | 15.3 | 21.8 | **0.70** |

Every row was produced by running some functional engine for π. The convergents were extracted from it, and the first row was re-encoded from it. No row comes from "the digits of π" as a free-standing object. Two lessons follow.

- **Individual address prefixes can beat the floor.** $355/113$ spends 15 bits of address to buy 22 bits of resolution. It can do this because the next partial quotient of π is 292, which makes this convergent exceptionally good. The floor is therefore not a law about individual addresses. Section 3 explains which statement it actually expresses.

---

## 3. The Floor, Revisited

### 3.1 What the counting argument really proves

RCC states that resolving a number to within $\varepsilon$ requires an address prefix of at least $\log_2(1/\varepsilon)$ bits. The proof is a counting argument, and it never mentions the real line.

- There are at most $2^B$ literal fractions of length $\le B$, so they distinguish at most $2^B$ cells.
- To resolve *every* cell of a unit interval at scale $\varepsilon$, you need about $1/\varepsilon$ distinct prefixes.

Note what is counted: literal fractions, not descriptions. A program of fixed size $K$ emits rungs of every length (§0), so the bound does not limit how much a short definition can determine. It limits how small numerators and denominators can be. That is why the floor is arithmetic, and why Diophantine approximation can refine it.

This is a *covering* bound. It holds:

- for the worst-case cell at each scale, and
- for all but a small fraction (by total width) of the cells at each scale.

Both clauses are statements about cells at a given scale. Neither mentions any particular number.

It does **not** bind a specific engine-defined number at every scale, as $355/113$ shows. Liouville numbers show this in the extreme.

- For $L = \sum_k 10^{-k!}$, the partial sums give $q = 10^{n!}$ and $\varepsilon \approx 10^{-(n+1)!}$.
- That is $c \approx n+1$ and $\kappa \to 0$ along those scales.
- $L$ is a legitimate example because its digits are given by a rule. Its formula *is* its engine.

At the other end there is a ceiling, but it is relative to an engine.

- Take any engine $E$ for $x$ with a proved error bound. The operator $R$ of §0 turns it into fractions with denominator about $1/\varepsilon$ that lie within $\varepsilon$ of $x$.
- So $B^*_x(\varepsilon) \le (2 + o(1))\log_2(1/\varepsilon)$ for every *addressable* $x$. A rational this good always exists, and it is computable from $E$.
- The ceiling is a property of the operator $R$, not a freestanding engine.
- It says nothing about numbers that no program defines, because there are none to talk about.

### 3.2 The best possible engine

For a fixed $x$, define the *ideal certificate profile*
$$B^*_x(\varepsilon) = \min\{\, B(p/q) : 0 < \lvert x - p/q\rvert \le \varepsilon \,\}.$$
Any engine $E$ for $x$ satisfies $B_E(\varepsilon) \ge B^*_x(\varepsilon)$. So $B^*_x$ is the floor *for this particular number*. The question is how it compares with the generic floor $\log_2(1/\varepsilon)$.

**Proposition.** For irrational $x$:
$$\mu(x) = 2 \iff B^*_x(\varepsilon) = (1 + o(1))\,\log_2(1/\varepsilon) \quad\text{as } \varepsilon \to 0.$$

*Sketch of (⇒).*

- **Lower bound.** If $\mu(x) = 2$, then for every $\delta > 0$ and all large $q$ we have $\lvert x - p/q\rvert \ge q^{-2-\delta}$. A certificate within $\varepsilon$ therefore needs $q \ge \varepsilon^{-1/(2+\delta)}$, which gives $B^* \ge (\tfrac{2}{2+\delta} - o(1))\log_2(1/\varepsilon)$.
- **Upper bound.** Let $p_n/q_n$ be the continued-fraction convergents. Standard estimates give $\lvert x - p_n/q_n\rvert \asymp 1/(q_n q_{n+1})$. Hence $\mu(x) = 2$ forces $q_{n+1} \le q_n^{1+o(1)}$.
  - Take $n$ minimal with $\lvert x - p_n/q_n\rvert \le \varepsilon$.
  - Then the previous convergent misses: $1/(q_{n-1}q_n) \gtrsim \varepsilon$.
  - Combined with $q_n \le q_{n-1}^{1+o(1)}$, this gives $q_n \le \varepsilon^{-1/2 - o(1)}$, so $B^* \le (1+o(1))\log_2(1/\varepsilon)$.

*Sketch of (⇐).* If $\mu(x) > 2$, pick $\nu$ with $2 < \nu < \mu(x)$. Then infinitely many $q$ satisfy $\lvert x - p/q\rvert < q^{-\nu}$. At $\varepsilon = q^{-\nu}$ this gives $B^* \lesssim \tfrac{2}{\nu}\log_2(1/\varepsilon)$, which is strictly below the floor.
**$B^*_x$ is attained by a functional engine, but only relative to $x$.** Let $x$ be irrational and defined by an engine $E$.
- The continued-fraction convergents and intermediate fractions of $x$ are computable from $E$. Each partial quotient needs only finitely many rungs, because $x$ never sits exactly on a rational boundary.
- A fraction of minimal height within $\varepsilon$ is a best approximation of the first kind. Every best approximation of the first kind is a convergent or an intermediate fraction.
- So an engine derived from $E$ realizes $B^*_x$ exactly.
The ideal profile is therefore not an infimum over unaddressable objects. It is a property of $x$'s definition. But the engine realizing it reads $x$ off $E$, so it measures $x$ rather than proving anything about it (§5, item 3).


### 3.3 Consequences for π

Read in RCC language, the theorem `OAI.PiExponent.main` says:

> **The ideal engine for π sits on the counting floor at every tolerance, with leading constant exactly 1.**

The README's sentence "an engine that spends only about that many bits is doing the best any method possibly could" is therefore, for π specifically and with the constant taken seriously, *equivalent to an open problem*.

What is actually proven comes from the Zeilberger–Zudilin bound $\mu(\pi) \le 7.1032\ldots$:
$$B^*_\pi(\varepsilon) \ge \left(\tfrac{2}{7.1032} - o(1)\right)\log_2(1/\varepsilon) \approx 0.28\,\log_2(1/\varepsilon).$$

Nobody can currently rule out certificates for π that are about 3.5 times cheaper than the floor, infinitely often.

Compare the cases where more is known:

- **Quadratic irrationals** such as $\sqrt 2$ have bounded partial quotients. For them $B^* = \log_2(1/\varepsilon) + O(1)$, which is the floor up to an *additive* constant.
- **Algebraic irrationals of degree ≥ 3**, by Roth's theorem, satisfy the Proposition. They are on the floor up to a $(1+o(1))$ *factor*. Whether the $O(1)$ version holds is open even for $\sqrt[3]{2}$.
- **Typical cells**: Khintchine's theorem, read finitarily, shows that at each scale all but a vanishing fraction of cells are consistent with the floor.
   - "Typical" here describes cells, not numbers. Every number that can be named is computable, and the computable numbers form a null set.
   - So Khintchine transfers to no specific constant. Each named number, π included, must be settled from its own program.

So RCC's claim that "algebraic irrationals reach the optimum" is supported by Roth's theorem. The analogous claim for π is the conjecture $\mu(\pi) = 2$.

---

## 4. Engines *Are* Irrationality Proofs

The deepest link runs in the other direction. Every known upper bound on $\mu(\pi)$ is proved by constructing a rational certificate engine with good bit efficiency.

### 4.1 The linear-forms lemma

The standard tool, in the form used by Hata, Salikhov and Zeilberger–Zudilin, is the following.

> Suppose there are integers $p_n, q_n$ such that:
> - $q_n x - p_n \neq 0$,
> - $\lvert q_n\rvert \le e^{\beta n + o(n)}$, and
> - $\lvert q_n x - p_n\rvert = e^{-\alpha n + o(n)}$ with $\alpha > 0$.
>
> Then $x$ is irrational and $\mu(x) \le 1 + \beta/\alpha$.

The sequence $p_n/q_n$ is a rational certificate engine. Its parameters translate directly:

- Error: $\varepsilon_n \approx e^{-(\alpha+\beta)n}$.
- Quality: $c = 1 + \alpha/\beta$.
- Efficiency: $\kappa = \dfrac{2\beta}{\alpha+\beta}$.

Eliminating $\alpha$ and $\beta$ gives a statement purely in RCC terms:

$$\boxed{\;\mu(x) \le \frac{2}{2 - \kappa_E}\;}\qquad\text{for any regular engine } E \text{ with } \kappa_E < 2.$$

Section 2 gave the reverse constraint, which holds for every engine:
$$\kappa_E \ge \frac{2}{\mu(x)}.$$

Together these produce the following dictionary:

| Engine efficiency $\kappa_E$ | What it proves about $x$ |
| :--- | :--- |
| $\kappa_E \ge 2$ (re-encoding quality or worse) | Nothing |
| $\kappa_E < 2$ | $x$ is irrational |
| $\kappa_E = 2 - 2/M$ | $\mu(x) \le M$ |
| $\kappa_E = 1$ | $\mu(x) = 2$, and $E$ is optimal |

The irrationality criterion is elementary. If $x = a/b$, then any $p/q \neq x$ satisfies $\lvert x - p/q\rvert \ge 1/(bq)$. That forces $c \le 1 + o(1)$, which means $\kappa \ge 2 - o(1)$.

The engine must also be **regular**. Its heights must grow geometrically, with no long gaps between rungs. This is the Diophantine counterpart of RCC's geometric tolerance ladder, and Section 6 shows why it matters.

### 4.2 The archetype: Apéry and ζ(3)

Apéry's proof that $\zeta(3)$ is irrational is an RCC engine in disguise.

- His recurrence produces $q_n \approx (1+\sqrt2)^{4n}$, after clearing denominators with $d_n^3$.
- The linear forms decay like $(\sqrt2 - 1)^{4n}$.
- Here $d_n = \operatorname{lcm}(1,\dots,n) = e^{n(1+o(1))}$ by the Prime Number Theorem.

This gives $\alpha = 4\log(1+\sqrt2) - 3 \approx 0.526$ and $\beta = 4\log(1+\sqrt2) + 3 \approx 6.526$. Hence:

- $c \approx 1.08$ and $\kappa \approx 1.85$, so the engine is only slightly better than rounding.
- That is enough: $\kappa < 2$ proves irrationality.
- It also yields $\mu(\zeta(3)) \le 13.42$.

The lesson is that an irrationality proof needs only a modest efficiency gain over rounding. What matters is that the gain persists along a regular ladder.

### 4.3 The history of μ(π) as an efficiency race

Converting each published bound into the efficiency $\kappa = 2 - 2/\mu$ that an idealized regular engine would need to reach it:

| Result | Bound on $\mu(\pi)$ | Equivalent $\kappa$ |
| :--- | :--- | :--- |
| Mahler (1953) | 42 | 1.952 |
| Hata (1993) | 8.0161 | 1.751 |
| Salikhov (2008) | 7.6063 | 1.737 |
| Zeilberger–Zudilin (2020) | 7.1032 | 1.718 |
| **Conjecture** (`PiExponent.lean`) | **2** | **1.000** |

Seventy years of work have lowered the efficiency of the best *provably regular* π engine from 1.95 to 1.72 bits per bit of accuracy. The conjecture asks for 1.00.

The gap is not about speed. Engines that reach the logarithmic tier are easy to find. The hard part is an engine whose certificates are *arithmetically lean*.

---

## 5. Where the Classical RCC Engines Sit

| Engine | RCC tier | $c$ (quality) | $\kappa$ (efficiency) | Diophantine content |
| :--- | :--- | :--- | :--- | :--- |
| Gregory–Leibniz for π | polynomial | $\to 0$ | $\to \infty$ | none |
| Wallis product for π | polynomial (worse) | $\to 0$ | $\to \infty$ | none |
| Decimal or binary re-encoding $R(E)$ of another engine $E$ | logarithmic (requires a functional $E$) | 1 | 2 | none; the cost of the encoding, not an engine in itself |
| Truncated $x \mapsto x + \sin x$ certificates | logarithmic | $\approx 1$ | $\approx 2$ | none expected (see §6) |
| Hata / Salikhov / Z–Z linear forms for π | logarithmic | ≈ 1.13–1.16 | ≈ 1.72–1.75 | proves $\mu(\pi) \le 7.10$ |
| Newton's method for $\sqrt2$ | logarithmic | 2 | 1 | Dirichlet-optimal, but sparse ladder |
| Pell / convergents for $\sqrt2$ | logarithmic | 2 | 1 | proves $\mu(\sqrt2) = 2$ |
| Convergents of π (derived from another π engine) | logarithmic | ≥ 2 infinitely often | $\to 1$ iff $\mu(\pi) = 2$ | exactly the open problem |

Several observations follow from the table.

1. **The polynomial tier is beneath rounding.**
   - The Leibniz partial sums after $N$ terms have denominators dividing $\operatorname{lcm}(1,3,\dots,2N-1)$, which is about $e^{2N}$ by the Prime Number Theorem. The error is only about $1/N$.
   - So $\varepsilon \approx 1/\log q$, which means $c \to 0$.
   - These engines lose to the trivial ceiling. Their RCC verdict is a statement about *waste*, not about π.
2. **Diophantine structure lives strictly inside the logarithmic tier.** RCC's tier system cannot separate re-encoding ($\kappa = 2$) from Zeilberger–Zudilin ($\kappa \approx 1.72$) or from the conjectured optimum ($\kappa = 1$). All three are "logarithmic". Making these distinctions requires the leading constant.
3. **Convergents of π are a circular engine.**
    - Computing them needs π to precision about $q^{-2}$, which some other *functional* engine must supply. A stored digit table would not do: it runs out, and extending it needs the very oracle in question.
   - This engine is valid and fast in RCC's sense.
   - Whether it lands on the floor at every rung is *equivalent* to $\mu(\pi) = 2$ (§3.2). It cannot prove anything, because it presupposes π.
4. **Existence vs. construction.**
    - Dirichlet's theorem, which gives $\mu \ge 2$, is a pigeonhole argument over finitely many fractions at each scale.
    - Relative to an engine for $x$ it is constructive: run the engine and search the finitely many candidates.
    - What it does not produce is an *independent* witness. It reads $x$ off its own engine, just as the convergents do.
    - Upper bounds on $\mu$ need engines whose quality is *proved* from their own program, not observed by comparison with another engine's output.
   - Part 2 of the Lean theorem combines both: Dirichlet for $\ge$, and the unproved part 1 for $\le$.

---

## 6. Why Fast Engines Are Poor Witnesses

The Simplest Increment is cubically convergent: $\lvert e_{n+1}\rvert \approx \lvert e_n\rvert^3/6$. It is natural to ask whether such a fast engine could help bound $\mu(\pi)$. The ladder-regularity condition says no, and the reason is instructive.

Generalize the lemma to an engine with:

- quality $c$, meaning $\lvert q_n x - p_n\rvert \approx q_n^{-(c-1)}$, and
- ladder ratio $\tau$, meaning $\log q_{n+1} \approx \tau \log q_n$.

Take any $p/q$ and let $n$ be minimal with $\lvert q_n x - p_n\rvert < 1/(2q)$. The integer $p q_n - q p_n$ is nonzero (the rigorous version uses two consecutive forms to guarantee this). Then:

- Since $p q_n - q p_n$ is a nonzero integer, $1 \le q\lvert q_n x - p_n\rvert + q_n\lvert qx - p\rvert$, which gives $\lvert qx - p\rvert \ge 1/(2q_n)$.
- Minimality of $n$ gives $q \gtrsim q_{n-1}^{c-1}$, while the ladder ratio gives $q_n \approx q_{n-1}^{\tau}$.

Combining these:
$$\mu(x) \le 1 + \frac{\tau}{c - 1}.$$

For a regular ladder ($\tau = 1$) this recovers $1 + \beta/\alpha$. Now apply it to fast engines.

- **Newton for $\sqrt 2$:** $\tau = 2$ and $c = 2$, which yields only $\mu \le 3$. The truth is 2. The engine is Dirichlet-optimal, yet its skipped rungs waste its leverage.
- **A cubic engine:** $\tau = 3$. Proving $\mu(\pi) \le 2$ this way would need $c \ge 4$ persistently. But persistent $c \ge 4$ would itself force $\mu(\pi) \ge 4$, a contradiction.

**So no cubically convergent engine can ever certify $\mu(\pi) = 2$ through the linear-forms route, however lean its fractions.**

This sharpens the remark in `analysis.md` that a cubic engine "can be bit-optimal up to a constant factor without saying anything about $\mu(\pi)$". The reason is not only that $x + \sin x$ produces bloated certificates when sine is truncated. Rapid convergence spaces the rungs geometrically in $\log\log(1/\varepsilon)$ rather than in $\log(1/\varepsilon)$. Diophantine leverage needs every rung.

In this sense, **speed and arithmetic insight pull in opposite directions.** RCC rewards few steps. Diophantine approximation rewards dense ladders of unusually lean fractions.

---

## 7. Shared Machinery

The two theories share more than vocabulary.

- **The Prime Number Theorem and $d_n = \operatorname{lcm}(1,\dots,n)$.**
  - In RCC, $d_n \approx e^n$ is the cost that drives Leibniz into the polynomial tier. The denominators of $\sum \pm 1/(2k+1)$ grow like $d_{2N}$ while the accuracy barely improves.
  - In irrationality proofs, the same $d_n$ is the *denominator tax*. The integrals behind §4, such as $\int_0^1 x^n(1-x)^n/(1+x^2)\,dx$ and their refinements, produce linear forms in π with rational coefficients. Clearing their denominators with powers of $d_n$ adds to $\beta$ and subtracts from $\alpha$.
  - When RCC's README says the framework "leans on the Prime Number Theorem", it is touching the same estimate that bounds every known measure for π.
- **Lowest terms and arithmetic savings.**
  - RCC measures *reduced* fractions. Common factors cancelled by the gcd lower the height.
  - The progress from Chudnovsky to Hata, Rhin–Viola, Salikhov and Zeilberger–Zudilin is mostly about proving that *more* such cancellation happens. Certain primes divide the binomial-type coefficients to unexpectedly high powers, and removing them lowers $\beta$.
  - The harness's insistence on exact reduction is therefore not pedantry. It is exactly where Diophantine improvements are found.
- **Hypergeometric structure.**
  - RCC's decidable criterion (a rational ratio of consecutive terms) singles out the same family that drives transcendence methods.
  - Apéry-like recurrences, Zeilberger's creative telescoping (used by Zeilberger–Zudilin to find recurrences for their integrals) and Padé approximants are all ways of *engineering* hypergeometric-type engines with $\kappa < 2$.
  - Similarly, the Simplest Increment engineers convergence *order* through Taylor coefficients, and the Diophantine program engineers convergence *efficiency* through arithmetic of coefficients.

---

## 8. A Ladder of Statements About π

The relationship becomes concrete when the known facts about π's certificates are ordered by strength:

1. **π has a logarithmic-tier engine.** True and easy. AGM or Chudnovsky is a functional engine, and re-encoding its output with $R$ gives $\kappa = 2$. A digit table on its own would not count, because it stops.
2. **π is irrational.** Proven (Lambert 1761). In RCC terms, some regular engine has $\kappa < 2$.
3. **μ(π) is finite.** Proven (Mahler 1953). In RCC terms, $B^*_\pi$ never falls below a positive multiple of the floor.
4. **μ(π) ≤ 7.1032.** Proven (Zeilberger–Zudilin 2020). In RCC terms, $B^*_\pi(\varepsilon) \ge (0.28 - o(1))\log_2(1/\varepsilon)$, witnessed by an engine with $\kappa \approx 1.72$.
5. **μ(π) = 2.** Open; this is `OAI.PiExponent.main`. In RCC terms, π's ideal engine sits on the counting floor with constant 1.
6. **π is badly approximable.** Open, and not even conjectured with confidence. In RCC terms, it would put π on the floor up to an *additive* $O(1)$, as for $\sqrt2$.

RCC's tier table operates at level 1. Every interesting Diophantine statement lies at levels 2–6, which RCC's growth-class verdicts cannot see.

---

## 9. Suggested Extensions to the RCC Reporting Contract

Some of this could be measured directly. Each new column below is cheap to compute from data the harness already produces.

| New column | Definition | What it detects |
| :--- | :--- | :--- |
| `quality_c` | Fitted slope of $\log(1/\varepsilon_n)$ against $\log q_n$ | The Diophantine exponent the engine achieves |
| `efficiency_kappa` | Fitted slope of $B_n$ against $\log_2(1/\varepsilon_n)$ | The leading constant hidden by "logarithmic" |
| `ladder_ratio_tau` | Median of $\log q_{n+1} / \log q_n$ | Whether the engine is regular enough to prove anything |
| `implied_mu_bound` | $1 + \tau/(c-1)$ when $c > 1$ | The irrationality-measure bound the engine would yield, if its behaviour were proved |
| `raw_vs_reduced_bits` | Bits before and after gcd reduction | Arithmetic savings, the main source of Diophantine progress |
| `derived_from` | The engine this one post-processes, if any | Re-encodings $R(E)$ and convergent extractors, which inherit another engine rather than witnessing anything |
**Admissibility.** Engines must be programs.
- A hard-coded table of digits or fractions is not an engine. It should be rejected, or reported as `oracle_backed = true` together with the program that produced the table.
- Re-encodings and other post-processing should be reported via `derived_from`. Their Diophantine columns describe the base engine's number, not an independent method.

These columns fit RCC's view that disagreement is the interesting output. They suggest the following flags:

- **$c > 1$ persistently:** the engine is behaving like an irrationality proof. Worth flagging.
- **$c \ge 2$ persistently on a regular ladder** for a constant whose measure is open: either a bug or a breakthrough. Almost certainly the former, so it should be treated as a red flag.
- **$c > 7.11$ persistently for π at large $q$:** this contradicts a theorem. It is definitely a bug. Isolated outliers such as $355/113$ ($c \approx 3.2$) are expected, so the fit must be asymptotic.

---

## 10. Caveats

- **Empirical fits are not bounds.** A harness can *observe* $\kappa \approx 1$ for π's convergents up to any computable scale. Proving it at all scales is the open problem. RCC measurements give evidence about $\mu$, never proof.
- **"Regular" hides real conditions.** The linear-forms lemma needs exact asymptotics, nonvanishing forms and geometric growth. The formulas in §4 and §6 are idealizations for orientation, not drop-in theorems.
- **κ ≈ 2/c is asymptotic.** For small certificates the offset $\log_2 \lvert x\rvert$ and rounding of bits matter. That is why the table in §2 does not satisfy the relation exactly.
- **RCC classifies engines, μ classifies numbers.** The bridge is $B^*_x$. It is attained by fractions derived from any engine for $x$ (§3.2). That derived engine presupposes $x$, however, so it measures the number rather than demonstrating anything about it.
- **Typical is not nameable.** Measure-theoretic results such as Khintchine's describe most *cells* at each scale. Every number that can be defined is computable, and the computable numbers form a null set. No such result settles the behaviour of a specific constant.
- **Bits measure height, not information.** A fixed program determines every rung with $O(\log\log(1/\varepsilon))$ extra description. The counting floor constrains the literal size of fractions, which is an arithmetic quantity, not how much a definition can say.

---

## 11. Takeaway

- **RCC asks:** how much does this engine pay per digit?
- **Diophantine approximation asks:** how little could any engine possibly pay?
- **Where the answers meet:**
- **What counts as an engine:**
   - Only a finite program defines a number.
   - A digit stream with no rule behind it cannot be extended without an oracle, so it addresses nothing.
   - Decimal truncation is a re-encoding $R(E)$ of some genuine engine $E$.
   - On the expensive side, re-encoding any engine with a proved error bound sets a ceiling of 2 bits per bit of accuracy. This is a property of the operator $R$, not an engine in itself.
   - On the cheap side, counting sets a floor of 1.
     - It binds most cells at every scale, but no specific number until that number's program is analysed.
     - It is a floor on arithmetic height, not on information.
  - An engine beating the ceiling on a dense ladder proves irrationality.
  - An engine matching the floor on a dense ladder proves $\mu = 2$.
   - Engines derived from another engine for the same number, such as re-encodings, convergents and Dirichlet searches, can *exhibit* this behaviour but cannot *prove* it.
- **For π**:
   - The best proven engine pays about 1.72. The conjecture formalized in `PiExponent.lean` says the true cost is 1.
   - Closing the gap requires a program for π whose rungs are proved lean on their own terms, not read off another π engine.
   - The x + sin x iteration, however elegant, is structurally the wrong kind of engine to close that gap. It converges too fast to leave a ladder dense enough to carry arithmetic information.
- "Counting floor" replaces "information-theoretic floor".
- $R(E)$ denotes decimal or binary re-encoding of an engine $E$. "Rounding" always means $R(E)$ for some functional $E$, never a bare digit source.
- **Decimal expansion is a re-encoding, not an engine.**
   - The digits $314159$ came from some π engine. Truncation only re-expresses that engine's output with denominator $10^n$.
   - The re-encoding has $c \approx 1$ and $\kappa \approx 2$, which is twice the floor.
   - The operator $R$ applies to any engine with a proved error bound, so this quality says nothing about the arithmetic of $x$. It is the cost of the encoding, charged on top of whatever engine supplied the digits.