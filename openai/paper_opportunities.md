# Publication and Research Opportunities

> **Scope.** An assessment of what in this cluster of drafts could be published now, what needs repair first, and which short- and mid-term research directions look worthwhile. It covers *The Simplest Increment* (`README.md`), RCC (`../RCC/README.md`), NAM (`../NAM/README.md`), TEL (`../TEL/README.md`), and the OpenAI-drafted bridges `analysis.md` and `diophantine_rcc.md`. It also considers `lean/ComparatorChallenges/PiExponent.lean`.
>
> **Bottom line.**
> - The mathematics in these drafts is almost entirely **known**. Their value is in synthesis, pedagogy, software and formalization, not in new theorems.
> - The strongest near-term paper is an **expository bridge between bit-length of rational approximations and the irrationality exponent**, built on `diophantine_rcc.md`.
> - Two quantitative claims in the RCC headline table look **wrong as stated** (Wallis, Nilakantha). One headline verdict in *The Simplest Increment* (logarithmic composite cost) depends on an unstated rounding step. All three must be fixed or verified before anything is submitted.
> - The most durable mid-term contributions are a **Lean formalization** of the κ–μ dictionary, and a **benchmark atlas** of measured engine efficiencies.

---

## 1. Inventory and Honest Novelty Assessment

| Artifact | Core content | Prior art it must engage | Novelty | Publishable as |
| :--- | :--- | :--- | :--- | :--- |
| *Simplest Increment* | $x \mapsto x+\sin x$ converges cubically to π, from three Taylor conditions | Folklore iteration. Order-from-derivative-conditions is Schröder (1870) and Traub (1964). Newton on $\sin$, $x \mapsto x-\tan x$, is also cubic at π for the same reason | Low as research, good as pedagogy | Classroom/expository note |
| RCC | Rank engines by bit-length of emitted rationals versus tolerance | Ko (1991) on real-number complexity; Brent (1976); binary splitting (Haible–Papanikolaou 1998); height theory | Low–moderate. The *reporting contract* and harness are the distinctive parts | Software or benchmark paper, after fixes |
| NAM | Numbers as stepped digit-stream machines | Turing (1936), Weihrauch (2000), coinductive streams (Rutten), signed-digit exact reals (Avizienis, Edalat–Potts), iRRAM (Müller) | Low. The synthesis is self-acknowledged | Essay or position paper |
| TEL | ℤ → ℚ → algebraic → transcendental → ℂ as one recurring mechanism | Standard field theory, Lindemann, Ostrowski | Low; pedagogical framing | Education or essay venue |
| `diophantine_rcc.md` | Dictionary $\kappa \approx 2/c$, $\mu \le 2/(2-\kappa)$, ladder-ratio penalty $\mu \le 1+\tau/(c-1)$, history of μ(π) as an "efficiency race" | Linear-forms irrationality criterion (Hata, Nesterenko-type criteria, Bugeaud 2004). The τ-version is a standard lemma for sequences of arbitrary growth | Moderate *as exposition*. The framing "speed and Diophantine leverage pull in opposite directions" is clean and quotable | **Best near-term paper** |
| `PiExponent.lean` | Formal statement of $\mu(\pi)=2$ | Mathlib (`irrational_pi`, Dirichlet approximation, continued fractions); curated collections of formalized open conjectures | N/A (it is an open problem) | Contribution to a conjecture collection; seed for a formalization project |

---

## 2. Defects to Fix Before Any Submission

These are listed in order of severity. Several of them sit inside headline claims.

### 2.1 Wallis is not in a worse tier than Leibniz under exact reduction

RCC's table gives Wallis bits $\sim (1/\varepsilon)\log(1/\varepsilon)$. In lowest terms the partial products are
$$P_N = \prod_{k=1}^{N}\frac{(2k)^2}{(2k-1)(2k+1)} = \frac{2^{4N}}{(2N+1)\binom{2N}{N}^2}.$$

- The numerator is a power of 2, and $\binom{2N}{N} \approx 4^N/\sqrt{\pi N}$. Reduction only removes a few powers of 2 (Kummer: as many as there are carries).
- So the reduced height is about $8N$ bits. That is **linear** in $N$, not $N\log N$.
- The error of $2P_N$ is about $\pi/(4N)$. Wallis therefore costs roughly $2\pi/\varepsilon \approx 6.3/\varepsilon$ bits.
- Leibniz costs about $\log_2 \operatorname{lcm}(1,\dots,2N) \approx 2.9N \approx 2.9/\varepsilon$ bits.

Wallis is worse by a **constant factor of about 2.2**. It is not worse by a log factor. RCC explicitly ignores constant factors, so by its own rules **Wallis and Leibniz land in the same tier**. The "load-bearing" Wallis-versus-Leibniz showcase needs one of two fixes:

- **Reframe it as a leading-constant phenomenon.** This fits well with the κ column proposed in `diophantine_rcc.md` §9 and arguably *strengthens* the case for reporting leading constants.
- **Show the harness measures something else.** For example, it may be measuring unreduced fractions. If so, that contradicts RCC's own "exact reduction" principle.

If the harness currently reports $N\log N$ for Wallis, the asymptotic fitter is probably misclassifying near-linear data. That is a good live test of the "disagreement is the interesting output" contract.

### 2.2 Nilakantha step count

The terms are $4/\bigl((2k)(2k+1)(2k+2)\bigr) \sim 1/(2k^3)$ and they alternate. So the error after $N$ terms is about $1/(2N^3)$, which means steps $\sim \varepsilon^{-1/3}$, not $\varepsilon^{-1/2}$. Bits follow the lcm of the denominators, about $2.9N$, so they also scale as $\varepsilon^{-1/3}$. Verify this against the harness and correct the table.

### 2.3 "Composite certificate cost lands in the logarithmic regime" (Simplest Increment)

This verdict depends on how the truncated-sine iteration handles heights.

- **Without intermediate rounding,** evaluating a degree-$d$ Taylor polynomial at a rational of $h$ bits gives about $d\cdot h$ bits. Heights multiply across steps.
  - With precision tripling, $d_k \gtrsim 3^k$ up to log factors, so bits grow roughly like $\prod_k 3^k = 3^{n^2/2}$.
  - That is $P^{\Theta(\log P)}$ in the target precision $P$. This is **super-polynomial in precision, not logarithmic in $1/\varepsilon$**.
- **With rounding between steps,** which is the operator $R$ of `diophantine_rcc.md`, cost is logarithmic with $\kappa \approx 2$. This is fine, but then the verdict is a property of $R$, not of the map.

State explicitly which variant the harness runs. If it is the rounded one, say that the logarithmic verdict is inherited from rounding.

### 2.4 Overclaims to soften

- **"Arguably the simplest closed-form iteration faster than quadratic."**
  - Newton's method on $\sin$, $x \mapsto x - \tan x$, is also cubic at π, because $\sin''(\pi)=0$ upgrades Newton's order.
  - The $x+\sin x$ iteration itself circulates widely as folklore.
  - Reframe the contribution as an exposition and cite Traub. Do a literature search for prior appearances before claiming minimality.
- **"π is precisely the object you are forced to adjoin when you close ℚ under sine and run it backwards."**
  - This needs a precise statement, for example "π is the least positive zero of $\sin$".
  - It also needs an acknowledgement that $\sin^{-1}(0)$ also contains 0, and that closure of ℚ under $\sin$ does not contain π.
- **"Algebraic irrationals reach the optimum" (RCC).**
  - Roth gives $(1+o(1))$-optimality of the *ideal* profile.
  - It does not show that the binomial-series engine has $\kappa = 1$. Measure κ for that engine; it is likely above 1.
- **"Information-theoretic floor."** `diophantine_rcc.md` has already renamed this the counting floor. The RCC README still uses the old wording in places. Harmonize the terminology everywhere.

### 2.5 Prior-art attribution in `diophantine_rcc.md`

- The ladder-ratio bound $\mu \le 1 + \tau/(c-1)$ is a standard irrationality-measure lemma for approximating sequences with $q_{n+1} \le q_n^{\tau+o(1)}$. Present it as known and cite a canonical source, such as Bugeaud's *Approximation by Algebraic Numbers* or a recent irrationality-measure paper that states it. Present the *consequence for fast engines* as the expository point.
- The "no cubic engine can certify μ(π)=2" claim is true **of this lemma only**.
  - More precisely: certifying μ=2 needs $c-1 \ge \tau$ and $c \le \mu = 2$, which forces $\tau \le 1$.
  - It is not a theorem about all possible proofs. Say so explicitly.
- The Proposition (μ=2 ⇔ $B^*_x = (1+o(1))\log_2(1/\varepsilon)$) is a reformulation. Label it as such.
- In the realization claim, "minimal height" mixes minimal $q$ (a best approximation of the first kind) with minimal $B=\log_2|p|+\log_2 q$. These agree only up to $O(1)$. Fix the wording.

### 2.6 Minor errata

- **`diophantine_rcc.md` §2** says "Two lessons follow" but lists only one.
- **§3.2** still says "ideal *certificate* profile". Change it to "address".
- **§5 table**: the Hata–Z–Z quality range should be $c \approx 1.14$–$1.16$, since $c = 1 + 1/(\mu-1)$.
- **§11** has an empty bullet ("Where the answers meet:"), and its trailing glossary bullets are misplaced.
- **`analysis.md` §3A** has a duplicated bullet: "Exact rational certificates…" next to "Rational addresses…".
- **`PiExponent.lean` part 2** needs a faithfulness review. Check that `sSup` cannot pick up Lean's junk value on an empty or unbounded set. The statement must not be vacuously true or trivially false for reasons unrelated to the mathematics. Do this before submitting the file anywhere.

---

## 3. Paper Candidates (Ranked)

### P1. "Bits per Digit: Rational Approximation Cost and the Irrationality Exponent" (expository article)

- **Thesis:** these quantities are the same object in different units:
  - the leading constant hidden inside "logarithmic bit cost" (κ),
  - the Diophantine quality (c), and
  - the irrationality exponent (μ).
  Convergence *speed* is orthogonal to, and even antagonistic to, Diophantine leverage.
- **Contents:**
  - the dictionary $\kappa \approx 2/c$ and $\mu \le 2/(2-\kappa)$;
  - the table converting Mahler → Zeilberger–Zudilin into κ;
  - Apéry's proof as an engine with $\kappa \approx 1.85$;
  - the τ-penalty and why Newton, Halley and cubic engines are poor witnesses;
  - worked examples: π convergents, √2, Liouville's number.
  - **Add $e$.** Its continued fraction $[2;1,2,1,1,4,1,1,6,\dots]$ is an explicit, *independent* program. Its ladder is regular ($\tau \to 1$) and its quality tends to 2. It proves $\mu(e)=2$ from its own program, while its unbounded partial quotients keep it off the $+O(1)$ floor. This makes it the one natural constant that realizes "level 5" of the §8 ladder, which is the perfect foil for π.
- **Venues:** *American Mathematical Monthly*, *Mathematical Intelligencer*, *Elemente der Mathematik*, *Mathematics Magazine*. Post to arXiv `math.HO` with cross-list `math.NT`.
- **Readiness:** medium. The content exists. It needs rigorous statements (nonvanishing via two consecutive forms), correct attributions (§2.5), and a shorter, less philosophical §0.
- **Effort:** 3–6 weeks.
- **Risk:** referees may see it as purely expository. That is acceptable for these venues if the writing is tight and the attributions are honest.

### P2. "x + sin x and the Anatomy of Cubic Convergence" (classroom note)

- **Contents:**
  - the three-condition theorem;
  - the perturbed-sine counterexample, which drops convergence from cubic to quadratic;
  - comparison with Newton on $\sin$ and with Halley;
  - general "derivative engineering": for any $g$ with $g(\alpha)=0$, $g'(\alpha)=-1$ and $g^{(j)}(\alpha)=0$ for $2 \le j < k$, the iteration has order $k$;
  - a short closing section on why speed does not buy arithmetic insight, pointing to P1.
- **Venues:** *College Mathematics Journal*, *Mathematical Gazette*, Monthly Notes.
- **Readiness:** high once the §2.4 overclaims are removed.
- **Effort:** 1–2 weeks.
- **Risk:** "known result". Mitigate by framing the note as pedagogy and citing Traub.

### P3. RCC Harness as a Benchmark and Software Paper

- **Contents:**
  - the reporting contract;
  - exact-arithmetic discipline;
  - the extended columns (`quality_c`, `efficiency_kappa`, `ladder_ratio_tau`, `implied_mu_bound`, `raw_vs_reduced_bits`, `derived_from`, `oracle_backed`);
  - a released dataset (CSV/JSON) for π, e, √2, ∛2, log 2, ζ(2), ζ(3) and Catalan.
- **Validation story:**
  - reproduce the published exponents of Apéry, Hata and Zeilberger–Zudilin numerically from their linear forms;
  - show the gcd/prime-power savings column recovering the known arithmetic improvements.
  This is the strongest evidence that the instrument measures something real.
- **Venues:** *Journal of Open Source Software* (software), *Experimental Mathematics* (if the validation section is substantial).
- **Readiness:** low–medium. It depends on fixing §2.1–2.3 and implementing the new columns.
- **Effort:** 1–3 months.

### P4. Formalized Statement Contribution: μ(π) = 2

- After the §2.6 faithfulness review, contribute `PiExponent.lean` to a public collection of formalized open conjectures. Also add companion statements:
  - $\mu(\pi) \le 7.1033$, as a "known result, sorry-free target";
  - $\mu(e) = 2$;
  - Roth's theorem.
- **Effort:** days.
- **Value:** visibility and a concrete entry point into P6.

### P5. Essays: NAM and TEL

- **NAM** fits an "ideas essay" track, such as ACM SPLASH *Onward! Essays*. Its honesty about undecidable equality, signed digits and p-adic locality is a selling point there.
- **TEL** fits mathematics-education venues (*For the Learning of Mathematics*, *Mathematics Teacher*) or the *Intelligencer*. The "rotation of a lattice gives sin(1)" passage needs a precise construction before submission.
- **Readiness:** medium. **Novelty:** low by design. Publish these only as essays, not as research.

### P6. A Constructivist Reading of Diophantine Approximation (philosophy of mathematics)

- **Thesis:** `diophantine_rcc.md` §0 makes three claims:
  - addresses must be generated by programs;
  - "almost every real" names no definable number;
  - the counting floor bounds height, not Kolmogorov information.
  Together these form a coherent Bishop/Markov-style reading of metric Diophantine approximation.
- **Venues:** *Philosophia Mathematica*, *Synthese*.
- **Risk:** high. It must engage seriously with the existing constructive-analysis and Markov-school literature, which the drafts currently do not cite.
- **Recommendation:** pursue this only after P1 is out.

---

## 4. Short-Term Research (0–3 Months)

1. **Re-run the RCC harness** with exact reduction and with the κ, c and τ columns. Confirm or refute the corrections in §2.1–2.2. Either outcome is reportable.
2. **Measure κ for the binomial √2 engine and for Pell convergents.** This shows concretely that "logarithmic tier" hides a factor between $\kappa = 1$ and $\kappa \approx 2$.
3. **Rebuild the linear-forms engines** of Hata, Salikhov and Zeilberger–Zudilin from their integrals in exact arithmetic.
   - Measure raw and reduced bits per rung.
   - Fit $\alpha$ and $\beta$, and check the implied μ bound against the published constants.
   - This is the validation backbone of P3.
4. **Add $e$, ∛2 and log 2 to the atlas.**
   - ∛2 is interesting because whether it sits on the floor up to $O(1)$ is open (bounded partial quotients are unknown). Plot its running maximum partial quotient as an empirical curiosity, clearly labelled as evidence, not proof.
5. **Locate the canonical citation** for the τ-lemma, and for any prior "speed versus irrationality-measure" remarks. If none exists, the observation is a small but genuine expository contribution to P1.
6. **Settle the x + sin x height question** (§2.3) rigorously. Give the bit growth with and without intermediate rounding as a short proposition in P2.

## 5. Mid-Term Research (3–18 Months)

1. **Lean formalization of the dictionary.**
   - Formalize the linear-forms criterion ($\kappa < 2$ on a regular ladder implies irrationality, plus the μ bound) and the τ-generalization on top of Mathlib's continued-fraction and Dirichlet results.
   - Stretch goal: a sorry-free formal proof that μ(π) is finite, via Mahler's or Hata's construction.
   - Either is a credible ITP or CPP paper. A formal finite bound for μ(π) would be a notable formalization result.
2. **Simultaneous approximation (the NAM "vectors" thread).**
   - Extend κ and c to engines emitting rational vectors or integer relations, where Dirichlet's exponent is $1 + 1/d$.
   - This connects RCC to benchmarking PSLQ and LLL. That is the use case already advertised in the RCC README.
3. **Effective finitary Khintchine.**
   - State and prove the cell-level statement sketched in §0 and §3.3: at each scale, the total width of cells admitting abnormally lean addresses is small.
   - The statement is probably routine, but having it written cleanly would anchor P6.
4. **Engine design: dense-ladder engines with lean heights.**
   - Ask whether Taylor-coefficient design (P2) can be combined with arithmetic design (Padé, hypergeometric).
   - Expectation: this rediscovers Padé and linear-forms methods. It is worth a few weeks only as a structured search using the harness, with the honest expectation of no improvement on 7.103.
   - **Do not frame this as an attack on μ(π)=2.**

## 6. What Not to Pursue

- Any claim that the cubic iteration, or any harness measurement, bears on μ(π)=2. §6 of `diophantine_rcc.md` already explains why it cannot.
- Claims of novelty for the order-of-convergence theorem, for NAM's protocol, or for the TEL ladder as mathematics.
- Headline comparisons that RCC's own constant-free tiers cannot distinguish (§2.1). Either adopt κ or drop the comparison.

## 7. Suggested Sequencing

| Window | Deliverable |
| :--- | :--- |
| Weeks 1–2 | Fix §2 defects; harness re-run; P4 submission |
| Weeks 2–4 | P2 submitted |
| Months 1–2 | P1 drafted with the e example and correct attributions; arXiv preprint |
| Months 2–4 | Harness columns plus linear-forms reproduction; P3 submitted |
| Months 3–6 | P5 essays; start Lean dictionary formalization |
| Months 6–18 | Formal finite μ(π) bound; simultaneous-approximation extension; P6 if warranted |

## 8. Literature Checklist

- **Iteration order:** Schröder (1870); Traub, *Iterative Methods for the Solution of Equations* (1964); Borwein & Borwein, *Pi and the AGM* (1987).
- **Irrationality measures:** Mahler (1953); Hata (1993, *Acta Arith.*); Rhin–Viola; Salikhov (2008); Zeilberger–Zudilin (2020); Apéry (1979) with van der Poorten's exposition; Bugeaud, *Approximation by Algebraic Numbers* (2004).
- **Classical metric theory:** Dirichlet, Roth (1955), Khintchine; the continued fraction of $e$ (Euler) and the proof that $\mu(e)=2$.
- **Complexity and computation:** Ko, *Complexity Theory of Real Functions* (1991); Weihrauch, *Computable Analysis* (2000); Brent (1976); Haible–Papanikolaou (1998); Bishop (1967).
- **Exact real arithmetic:** Avizienis (signed digits), Edalat–Potts, Müller (iRRAM), Rutten (stream coalgebra).

## 9. Process Notes

- Parts of the bridge documents were AI-drafted. Most journals and arXiv require disclosure of substantive AI assistance and require the human authors to take responsibility for every claim. Check each venue's policy, and verify every number in the drafts independently. §2 shows the drafts contain real errors.
- Release the harness, its exact version, and the raw CSV/JSON with each paper, so that every table can be regenerated.