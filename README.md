<h1 align="center">Sampling &amp; Survey Design Calculator</h1>

<p align="center">
  <em>Everything that happens before the dataset exists.</em>
</p>

<p align="center">
  <img alt="single file" src="https://img.shields.io/badge/single--file-HTML-1B6E6E?style=flat-square">
  <img alt="no build" src="https://img.shields.io/badge/build-none-1B6E6E?style=flat-square">
  <img alt="no server" src="https://img.shields.io/badge/server-none-1B6E6E?style=flat-square">
  <img alt="license" src="https://img.shields.io/badge/license-MIT-7C5A17?style=flat-square">
</p>

---

A single-file HTML tool for the design stage of a survey project — sample size,
allocation, power, response rates, and design weights. Opens in any modern
browser. No build step, no server, no dependencies beyond a Google Fonts link.
State is not persisted; a refresh resets everything.

> [!TIP]
> **To open it:** save the HTML file (e.g. `sampling-calculator.html`) and
> double-click it. No installation, no network connection required after the
> first load of the fonts.

---

## Contents

- [What it is not](#what-it-is-not)
- [The five cards](#the-five-cards)
  - [Card 01: Sample size](#card-01-sample-size)
  - [Card 02: Sampling design](#card-02-sampling-design)
  - [Card 03: Power & MDE](#card-03-power--mde)
  - [Card 04: Response rates](#card-04-response-rates)
  - [Card 05: Design weights](#card-05-design-weights)
- [Workflow](#workflow)
- [Common mistakes](#common-mistakes)
- [Formula reference](#formula-reference)
- [Deliberately not included](#deliberately-not-included)
- [Browser support](#browser-support)
- [Customising](#customising)

---

## What it is not

It is not an analysis tool. It computes design parameters — sample sizes,
allocations, power, weights — from parameters you supply. It does not read data,
estimate variances, run regressions, or produce confidence intervals on real
estimates. Those belong in R, Stata, or Python.

It also does not persist anything. If you are midway through a set of parameters
and want to keep them, use the **copy parameters** button on cards 01 and 03, or
screenshot the panel.

---

## The five cards

Cards 01 and 03 are independent. Card 02 links to card 01's output. Cards 04 and
05 stand alone.

### Card 01: Sample size

**Answers:** how many completed interviews do I need?

Two modes:

| Mode | Use for | Formula |
| :--- | :--- | :--- |
| **Proportion** | Estimating a percentage | $n = z^2 p(1-p) / e^2$ |
| **Mean** | Estimating an average | $n = z^2 \sigma^2 / e^2$ |

Set $p = 0.50$ if you have no prior; it is the most conservative choice. The
mean mode requires a standard deviation estimate from a pilot, prior work, or a
defensible assumption.

Three adjustments apply in sequence, each shown in the breakdown panel:

1. **Finite-population correction** — applied automatically when base $n$
   exceeds 5% of $N$. Leave $N$ blank if the population is effectively
   unlimited.
2. **Design effect** — multiplies required $n$ by DEFF. Leave at 1.0 for simple
   random sampling; use 1.5–2.5 for typical cluster designs, or let card 02's
   cluster tab compute it.
3. **Response rate** — converts completed interviews into contacts. At 40%
   response, you need 2.5× as many issued units as completes.

The headline number is **completed interviews**. The sub-line beneath it gives
the contact count when response rate is below 100%.

### Card 02: Sampling design

**Answers:** given a total $n$, how do I distribute or select it?

A shared $n$ field at the top is linked to card 01's output. Editing it directly
overrides the link; click **relink** to reconnect.

<details>
<summary><b>Simple random</b></summary>

Selection probability $n/N$, sampling fraction, and a button that draws $n$
distinct 1-indexed integers from $1$–$N$ via partial Fisher–Yates. Copyable.
Useful when the frame is a simple numbered list.

</details>

<details>
<summary><b>Systematic</b></summary>

Sampling interval $k = N/n$ with three rounding modes:

- **Floor** — sample ≥ $n$
- **Ceil** — sample ≤ $n$
- **Circular** — exactly $n$, wrapping past the end of the frame

The tab warns when the mode gives you a different sample size than requested.
Watch for periodicity in the frame.

</details>

<details>
<summary><b>Stratified</b></summary>

A dynamic table of strata with population $N_h$, standard deviation $S_h$, and
per-unit cost $c_h$. Four allocation methods: proportional, equal, Neyman, and
optimal-with-cost. Any stratum whose allocation exceeds $N_h$ is capped at $N_h$
and the remainder redistributed. Allocations are rounded by largest-remainder so
the $n_h$ are integers summing exactly to $n$.

For proportions, set $S_h = \sqrt{p_h(1-p_h)}$. For a 50/50 split, $S_h = 0.5$.

</details>

<details>
<summary><b>Cluster</b></summary>

Design effect from average cluster size $m$ and intra-cluster correlation $\rho$.

$$\text{DEFF} = 1 + (m-1)\rho$$

Translates between target effective $n$ and number of clusters to select. The
notice warns when DEFF exceeds 4 and points at cluster size as the lever.

</details>

### Card 03: Power & MDE

**Answers:** can I detect the effect I care about?

Four test types: two proportions, two means, one proportion (vs. a reference),
paired means. Two modes:

- **Sample size** — enter the effect you want to detect, read the required $n$.
- **Detectable effect** — enter $n$, read the smallest effect you can detect.

One- or two-sided. Optional cluster adjustment via the same DEFF formula as
card 02's cluster tab.

Cohen's $d$ interpretation (0.2 / 0.5 / 0.8) appears when you supply $\sigma$ and
$\Delta$. Treat it as context, not guidance — the effect size that matters is
the one your client cares about, not a conventional threshold.

> [!IMPORTANT]
> **Card 03's $n$ is not the same quantity as card 01's $n$.** Card 01 sizes for
> precision on a single estimate; card 03 sizes for power against a comparison.
> They will disagree, and both are correct for their respective questions.

### Card 04: Response rates

**Answers:** what were my AAPOR rates, and how many units do I need to issue?

Enter the disposition counts from a completed round of fieldwork: completes (I),
partials (P), refusals (R), non-contacts (NC), other (O), unknown household
(UH), unknown other (UO), and the estimated eligibility rate $e$ (0.50 is the
usual default).

The panel returns all thirteen standard rates:

| Group | Codes |
| :--- | :--- |
| Response rates | RR1–RR5 |
| Cooperation rates | COOP1–COOP4 |
| Refusal rates | REF1–REF2 |
| Contact rates | CON1–CON2 |

The projection panel converts a target completes count into a release size at
RR2 and RR3 — useful at the planning stage and again if a project is running
short.

The five response-rate definitions differ only in how they handle partials (in
or out) and unknown eligibility (denominator-inflating or probabilistically
allocated). RR1 is the most conservative, RR5 the least. AAPOR recommends
reporting all five, or at minimum RR3 and COOP2 for most telephone and
face-to-face surveys.

### Card 05: Design weights

**Answers:** what weight does each respondent get?

Per-stratum table with population $N_h$, respondents $n_h$, response rate
$RR_h$, and an optional external control total for post-stratification.

The final weight is population ÷ respondents — which already nets out
non-response, because $n_h$ is the number of **completes**, not issued units. If
you supply a control total, that replaces $N_h$ in the numerator and
post-stratifies to the external benchmark.

Summary panel gives:

- Weight range (min–max)
- Mean weight
- Coefficient of variation
- **Kish design effect:**

$$D_{\text{eff}} = \frac{n \cdot \sum_h (N_h^2 / n_h)}{N_{\text{total}}^2}$$

Kish DEFF measures how much precision unequal weights cost relative to a simple
random sample of the same size. A value above 3 usually warrants trimming
extreme weights or collapsing sparse strata. The notice fires when DEFF > 3 or
CV > 50%.

---

## Workflow

Typical sequence for a new project:

1. **Card 01** — decide precision target, confidence, and any design effect.
   Read off completed interviews needed.
2. **Card 03** — check that the sample is large enough to detect the effect the
   client cares about. If not, negotiate the effect size or the budget.
3. **Card 02** — allocate across strata or clusters. If using cluster sampling,
   leave card 01's DEFF at 1 and let the cluster tab do the translation, or you
   will inflate twice.
4. **Card 04** — plan the release size from expected response rates. This is the
   number that goes into the fieldwork budget.
5. **Card 05** — after fieldwork, compute the design weights to hand off to
   analysis. The Kish DEFF goes into the methodology section.

---

## Common mistakes

> [!WARNING]
> **Applying DEFF twice.** If you set DEFF > 1 in card 01 *and* use card 02's
> cluster tab, you have inflated twice. Pick one location for the design effect.

**Forgetting the response-rate inflation.** Card 01's headline number is
*completed interviews*, not issued units. If you need 400 completes at 35%
response, you issue 1,143 units. Card 04's projection panel handles this if you
want it in one place.

**Treating card 03's $n$ as interchangeable with card 01's.** They answer
different questions. Precision on an estimate is not the same as power to detect
a difference. A survey can be well-powered for a comparison and under-powered
for subgroup estimates, or vice versa.

**Using $N$ when the population is effectively unlimited.** The
finite-population correction only matters when the sample is a sizeable fraction
of $N$. A national survey of 1,000 in a country of 40 million does not benefit
from it; a survey of a 3,000-person employee base does.

**Reading Kish DEFF as a variance estimate.** It measures the precision cost of
*unequal weighting*, not the precision of any particular estimate. The actual
design effect depends on which variable you are estimating.

---

## Formula reference

### Card 01 — Sample size

$$
\begin{aligned}
\text{proportion:}\quad & n = \frac{z^2 p(1-p)}{e^2} \\[4pt]
\text{mean:}\quad & n = \frac{z^2 \sigma^2}{e^2} \\[4pt]
\text{FPC:}\quad & n_{\text{adj}} = \frac{n}{1 + (n-1)/N} \\[4pt]
\text{contacts:}\quad & n_{\text{contacts}} = \frac{n_{\text{adj}} \cdot \text{DEFF}}{\text{response rate}}
\end{aligned}
$$

### Card 02 — Allocation

$$
\begin{aligned}
\text{proportional:}\quad & n_h = n \cdot \frac{N_h}{N} \\[4pt]
\text{equal:}\quad & n_h = \frac{n}{H} \\[4pt]
\text{Neyman:}\quad & n_h = n \cdot \frac{N_h S_h}{\sum N_h S_h} \\[4pt]
\text{optimal:}\quad & n_h = n \cdot \frac{N_h S_h / \sqrt{c_h}}{\sum N_h S_h / \sqrt{c_h}} \\[4pt]
\text{systematic:}\quad & k = N/n,\ r \in [1, k],\ r,\ r+k,\ r+2k, \ldots \\[4pt]
\text{cluster DEFF:}\quad & 1 + (m-1)\rho
\end{aligned}
$$

### Card 03 — Power

$$
\begin{aligned}
\text{two props:}\quad & n_{\text{per group}} = \frac{(z_\alpha + z_\beta)^2 \bigl[p_1(1-p_1) + p_2(1-p_2)\bigr]}{\Delta^2} \\[4pt]
\text{two means:}\quad & n_{\text{per group}} = \frac{2(z_\alpha + z_\beta)^2 \sigma^2}{\Delta^2} \\[4pt]
\text{one prop:}\quad & n = \frac{(z_\alpha + z_\beta)^2 p(1-p)}{\Delta^2} \\[4pt]
\text{paired:}\quad & n_{\text{pairs}} = \frac{(z_\alpha + z_\beta)^2 \sigma_d^2}{\Delta^2}
\end{aligned}
$$

### Card 04 — AAPOR

$$
\begin{aligned}
\text{RR1} &= \frac{I}{I + P + R + NC + O + UH + UO} \\[4pt]
\text{RR3} &= \frac{I}{I + P + R + NC + O + e(UH + UO)} \\[4pt]
\text{COOP2} &= \frac{I + P}{I + P + R + O} \\[4pt]
\text{CON1} &= \frac{I + P + R + O}{I + P + R + O + NC + UH + UO}
\end{aligned}
$$

### Card 05 — Weights

$$
\begin{aligned}
\text{design weight:}\quad & w_h = \frac{N_h}{n_h} \\[4pt]
\text{post-stratified:}\quad & w_h = \frac{N_h^{\text{control}}}{n_h} \\[4pt]
\text{Kish DEFF:}\quad & D_{\text{eff}} = \frac{n \cdot \sum_h (N_h^2 / n_h)}{N_{\text{total}}^2}
\end{aligned}
$$

> All power and sample-size formulas are normal approximations. Fine for
> planning; slightly optimistic at very small $n$ or extreme proportions.

---

## Deliberately not included

**Raking / IPF.** Iterative proportional fitting requires marginal control
totals and a convergence loop. Use R's `survey::rake()` or `anesrake`.

**Weight trimming.** Winsorising or trimming extreme weights is a judgment call
that depends on the weight distribution in the actual data. Inspect it in
analysis.

**Variance estimation under complex designs.** Taylor linearisation, jackknife,
and BRR all need the dataset. R's `survey` package handles all three; this tool
stops before it.

**Imputation.** Hot-deck, regression, and multiple imputation are
analysis-stage operations. Rubin's rules for combining MI estimates could be a
small calculator, but it is a narrow use case.

**Model-based sampling and MRP.** A different inferential paradigm that does not
fit a design-stage tool.

---

## Browser support

Tested in current Chrome, Firefox, Safari, and Edge. Requires
`navigator.clipboard` for the copy buttons (falls back to `document.execCommand`
where unavailable). Uses `prefers-color-scheme` for dark mode. No JavaScript
modules, no build step.

---

## Customising

The file is one HTML document with an inline `<style>` and `<script>`.
Everything is in the five card IIFEs at the bottom of the script.

- To change defaults, edit the `DEFAULTS` / `POW_DEFAULTS` objects inside the
  relevant card.
- To add strata on card 02, edit the `strata` array in the stratified tab's
  IIFE.
- To change the weight defaults on card 05, edit its `strata` array.

---

<p align="center">
  <sub>
      Statistician<br>
     SHAKALIMA.J MSc
  </sub>
</p>
