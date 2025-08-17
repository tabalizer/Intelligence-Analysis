# 🛡️ Bayesian Cheat Sheet for Conventional Intelligence (Do-This Guide)

**Use when:** You must decide under uncertainty (e.g., issue warning, increase collection, brief leadership) from mixed-source reporting.

---

## 0) What model fits my question? (picker)
- **Binary judgment (credible/not; presence/absence)** → **Beta–Binomial**
- **Event **counts** per period (incidents/week, sightings/day)** → **Poisson** (use **Negative Binomial** if counts are “bursty”)
- **Magnitude (continuous) with many observations** → **Normal** (only if needed; most intel use-cases are binary or counts)

---

## 1) Six-step workflow (follow in order)
1) **Define action** you might take (watch, warn, escalate) and its **cost**.
2) **Pick model** from the picker above.
3) **Set a prior from baseline** (see §2).
4) **Update with today’s data** (see §3).
5) **Decide with a rule** tied to action cost (see §4).
6) **Record a short note** (template in §6) and **monitor**.

---

## 2) How to set a prior from intel baselines (simple & defensible)

### A) For binary judgments (Beta prior for a true rate `p`)
- Use historical “credible fraction” for similar claims/sources:
  - Baseline rate `m` (0–1) and **prior strength** `s` (“equivalent past cases” you trust)
  - Prior: **Beta(α, β)** with `α = m*s`, `β = (1−m)*s`
  - Typical `s`: **20** (light), **100** (moderate), **300** (strong history)

**Heuristic from Admiralty Code (adjust to your policy):**
- Source Reliability **A/B/C/D** → pick `s = 150/100/60/30`
- Information Credibility **1/2/3/4/5/6** → pick `m = 0.8/0.65/0.5/0.35/0.2/0.1`

> Example: B2 → `m=0.65`, `s=100` ⇒ prior `Beta(65,35)`

### B) For counts (Gamma prior on rate `λ` for Poisson)
- Baseline mean incidents per period `λ0`, and prior strength `s` (“equivalent periods”)
- Prior: **Gamma(α, β)** with `α = λ0*s`, `β = s` (mean stays `λ0`)
- **Overdispersion check:** from recent data compute mean `μ` and variance `v`
  - If `v/μ ≤ ~1.5` → Poisson OK; **else use Negative Binomial** (NB) in §3C

---

## 3) Update rules (the “math” you actually use)

### A) Beta–Binomial (binary credibility, success/fail)
- Observe `k` credible out of `n` checks
- **Posterior:** `Beta(α + k, β + n − k)`
- Report: posterior mean of `p` and **90% interval**

### B) Poisson via Gamma–Poisson (incident counts)
- Observe `x` events over exposure `t` periods (often `t=1`)
- **Posterior on rate λ:** `Gamma(α + x, β + t)`
- **Posterior predictive** for next period gives `P(next count ≥ threshold)`

### C) Negative Binomial (counts with extra variance; “bursty”)
- Use NB parameterized by **mean** `μ` and **alpha** (overdispersion):
  - `Var = μ + μ²/alpha` (smaller alpha ⇒ more bursty)
- Practical: start with `alpha ≈ 1` if unsure; fit/tune as data accrue
- Decision uses **posterior predictive** like Poisson, but with NB

---

## 4) Decision rules tied to action cost (pick 1 and stick to it)

### Probability threshold (binary judgments)
- **Low-cost** actions (watchlist, targeted collection): act if `P(p > τ) > 0.7` (e.g., `τ = 0.3–0.5`)
- **Medium-cost** (stakeholder note/limited warning): `> 0.8`
- **High-cost/regulated** (public warning/resource shift): `≥ 0.9`

### Spike rule (counts)
- Set a threshold (e.g., **2×** baseline next period)
- Act if `P(next count ≥ threshold) > 0.7` (low cost), `> 0.8` (medium), `≥ 0.9` (high)

### Expected Value (EV) sanity check (optional but good)
- `EV = (Probability of adverse outcome) × (Impact) − (Action cost)`
- If **EV > 0**, action is justified; pair with a probability rule above for stability

---

## 5) Worked micro-examples (plug and go)

### Example 1 — HUMINT claim credibility (Beta–Binomial)
- Source B2 ⇒ `m=0.65`, `s=100` → prior `Beta(65,35)`
- Today: `k=6` credible corroborations out of `n=12` checks
- Posterior: `Beta(71,41)` → compute `P(p > 0.5)`
- If `P > 0.9` and action is medium-cost ⇒ **issue stakeholder note**

### Example 2 — Protest activity spike (counts)
- Baseline 4 incidents/day, choose `s=10` → prior `Gamma(40,10)`
- Today `x=9` (t=1) → posterior `Gamma(49,11)`
- Posterior predictive: `P(next day ≥ 8) = 0.76` ⇒ **shift collection/watch** (0.7 rule)

### Example 3 — Choosing between two narratives (A vs B) by corroboration rate
- Both start `Beta(1,1)`
- A: `k=12/30` → `Beta(13,19)`; B: `k=18/35` → `Beta(19,18)`
- Compute `P(p_B > p_A)`; if `> 0.9` and medium-cost action ⇒ **brief B as more likely**

---

## 6) Analyst note template (paste & fill)
- **Decision:** _(what you did)_
- **Rule:** _(e.g., P>0.8 spike rule; or EV>0)_
- **Model:** _(Beta–Binomial / Poisson / NB)_
- **Prior:** `m=__`, `s=__` → Beta(α,β) **or** `λ0=__`, `s=__` → Gamma(α,β)
- **Data:** `k=__/n=__` **or** `x=__` over `t=__`
- **Posterior key metric:** `point [low, high]`
- **Probability used:** `P(p>τ)=__` **or** `P(next≥threshold)=__`
- **Rationale (1–2 lines):** _why sufficient for action_
- **Monitoring:** _what & when you re-check_

---

## 7) ACH plug-in (optional but simple)
- Treat each hypothesis as an “arm.” Score each new evidence item **E** with a **Likelihood Ratio (LR)** scale:
  - Strongly favors H: **LR 5**
  - Moderately favors H: **LR 3**
  - Slightly favors H: **LR 1.5**
  - Neutral: **LR 1**
  - Slightly against H: **LR 1/1.5**
  - Moderately against H: **LR 1/3**
  - Strongly against H: **LR 1/5**
- Start from prior odds (can be equal). **Posterior odds = prior odds × LR₁ × LR₂ × …**
- Normalize to get posterior probabilities; apply the same **probability/EV rules** as above.

---

## 8) Pitfalls & fixes
- **Bursty counts using Poisson** → switch to **Negative Binomial**
- **Unrealistic priors** → do a quick **prior predictive** sanity check; re-center/widen
- **Tiny samples flipping decisions** → keep prior strength modest (20–100) and require higher probability thresholds for high-cost actions
- **Moving thresholds** → set thresholds **by policy & action cost** up front; don’t change them mid-stream

---

## 9) What to deliver up-chain (one slide/paragraph)
- The **decision** and the **rule** that triggered it
- The **probability** (or predictive spike chance) and a **90% interval**
- A brief **EV** note if stakes are high
- **Next check-in** (24–72h) and what will change the decision

---

**Provenance:** Standard Bayesian operational practice and structured analytic techniques (ACH, 5W1H).
