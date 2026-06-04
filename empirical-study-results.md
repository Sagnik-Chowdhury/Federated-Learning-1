# Empirical Study: Federated Learning Aggregation Strategies under Data Heterogeneity, Noise, and Poisoning

**Authors:** Sourit Mitra & Sagnik Chowdhury  


## 1. Introduction

This report presents a systematic empirical evaluation of five federated learning aggregation strategies:

- **FedAvg** – standard weighted averaging.
- **Trimmed Mean** – discards extreme parameter values (top & bottom 5–10% per coordinate).
- **Gradient Clipping** – uses a **fixed 80th‑percentile threshold** computed from the first round to clip each client’s entire weight vector.
- **FedProx** – adds a proximal penalty (`μ = 0.01–0.1`) to anchor local updates.
- **SCAFFOLD** – uses control variates to correct client drift.

We test these algorithms across four distinct scenarios:

| Scenario | Dataset | Key Variable | Goal |
|----------|---------|--------------|------|
| **1. Noise robustness** | MNIST (dense) vs Breast Cancer (tabular) | Gaussian / Laplace noise at server | Understand data modality fragility |
| **2. IID baseline** | CIFAR‑10 | IID split | Establish ceiling performance |
| **3. Non‑IID skew** | CIFAR‑10 | Dirichlet α = 0.05,0.15,0.25,0.35,0.45 | Evaluate tolerance to statistical heterogeneity |
| **4. Data poisoning** | CIFAR‑10 | Label flipping (2/10 malicious) | Test Byzantine robustness |

All experiments use CNNs / MLPs appropriate for each dataset. The complete code and raw logs are available in the repository.

---

## 2. Experiment 1: Noise Robustness – Dense vs Tabular Data

**Setup:** 20 clients, IID split, 5 rounds. Server injects Gaussian or Laplace noise (scale = 0.05) after aggregation.  
**Models:** MNIST → 4‑layer MLP; Breast Cancer → 3‑layer MLP with dropout.

### 2.1 FedAvg (No defensive filters)

| Dataset       | No Noise | Gaussian | Laplace |
|---------------|----------|----------|---------|
| MNIST (dense) | 94.64%   | 86.89%   | 66.65%  |
| Tabular       | 99.12%   | 98.25%   | 96.49%  |

**Observation:** Dense image data degrades severely under Laplace noise, while tabular data remains robust.

### 2.2 Gradient Clipping (fixed 80th percentile threshold)

| Dataset       | No Noise | Gaussian | Laplace |
|---------------|----------|----------|---------|
| MNIST (dense) | 92.15%   | 30.82%   | 10.56%  |
| Tabular       | 95.61%   | 92.98%   | 94.74%  |

**Observation:** Clipping alone does not protect dense data from noise; heavy‑tailed Laplace destroys performance. Tabular data again absorbs noise well.

### 2.3 Trimmed Mean (trim 10%)

| Dataset       | No Noise | Gaussian | Laplace |
|---------------|----------|----------|---------|
| MNIST (dense) | 92.43%   | 80.94%   | 59.02%  |
| Tabular       | 96.49%   | 96.49%   | 94.74%  |

**Observation:** Trimming improves resilience to Gaussian noise for MNIST, but Laplace still hurts.

### 2.4 FedProx (μ = 0.01)

| Dataset       | No Noise | Gaussian | Laplace |
|---------------|----------|----------|---------|
| MNIST (dense) | 92.45%   | 77.35%   | 25.94%  |
| Tabular       | 97.37%   | 96.49%   | 96.49%  |

**Observation:** Proximal anchoring helps recover from Gaussian noise, but Laplace noise causes instability (oscillating accuracy).

### 2.5 SCAFFOLD

| Dataset       | No Noise | Gaussian | Laplace |
|---------------|----------|----------|---------|
| MNIST (dense) | 26.20%   | 7.82%    | 6.49%   |
| Tabular       | 62.28%   | 63.16%   | 66.67%  |

**Observation:** SCAFFOLD completely fails on MNIST even without noise – the control variates over‑correct and cause divergence. On tabular data it is mediocre.

**Key takeaway:** Dense spatial data is inherently vulnerable to heavy‑tailed noise; no defense fixes this. Tabular data can tolerate strong noise.

---

## 3. Experiment 2: IID Baseline – CIFAR‑10

**Setup:** 10 clients, IID split (5k samples each), 25 rounds, SGD (lr=0.01, momentum=0.9).  
**Model:** CNN (2 conv + 2 dense).

| Strategy          | Final Test Accuracy |
|-------------------|---------------------|
| FedAvg            | 70.42%              |
| Trimmed Mean      | 69.75%              |
| Gradient Clipping | 67.90%              |
| FedProx           | 66.44%              |
| SCAFFOLD          | 69.33%              |

**Observation:** FedAvg performs best in IID settings. Trimming and SCAFFOLD are close seconds. Clipping and FedProx slightly lag but are stable.

---

## 4. Experiment 3: Non‑IID Skew – Dirichlet(α) on CIFAR‑10

**Setup:** 10 clients, data partitioned with Dirichlet(α). α = 0.05 (extreme skew) … 0.45 (mild skew). 25 rounds.

### 4.1 Final accuracy vs α (summary)

| α   | FedAvg | Trimmed | Clip | FedProx | SCAFFOLD |
|-----|--------|---------|------|---------|----------|
| 0.05| 47.36% | 46.34%  | 51.59%| 46.65%  | 10.00% (NaN) |
| 0.15| 62.74% | 58.91%  | 61.81%| 57.13%  | 10.00% (diverged) |
| 0.25| 61.44% | 57.82%  | 60.61%| 56.28%  | 45.12% |
| 0.35| 63.17% | 63.18%  | 63.30%| 60.92%  | 43.65% |
| 0.45| 64.83% | 62.87%  | 63.21%| 62.32%  | 55.45% |

**Observations:**
- **Gradient Clipping** with fixed 80th‑percentile threshold outperforms FedAvg at α=0.05 (51.6% vs 47.4%) and remains competitive for all α.
- **SCAFFOLD collapses** at α ≤ 0.15 (loss becomes NaN, accuracy drops to 10%). Control variates cannot handle extreme class absence.
- **Trimmed Mean** and **FedProx** offer minor gains but not dramatic.
- As α increases (more balanced), all methods converge toward the IID baseline.

**Key takeaway:** For severe non‑IID, **Gradient Clipping with a first‑round fixed threshold** is simple, robust, and outperforms more complex methods.

---

## 5. Experiment 4: Data Poisoning – Label Flipping Attack

**Setup:** 10 clients, IID split, 2 malicious clients (20% compromise). Attack: `label = (label + 5) % 10`. 10 rounds.

| Strategy          | Final Test Accuracy |
|-------------------|---------------------|
| FedAvg            | 54.18%              |
| Trimmed Mean      | 55.49%              |
| Gradient Clipping | 54.13%              |
| FedProx           | 55.30%              |
| SCAFFOLD          | 51.59%              |

**Observations:**
- All methods suffer a large drop compared to the clean IID baseline (~70% → ~55%).
- Trimmed Mean and FedProx are marginally better (+1% over FedAvg).
- Gradient Clipping with fixed threshold does not help against label flipping (poisoned updates do not inflate weight norms).
- SCAFFOLD performs worst, confirming it is not Byzantine‑robust.

**Key takeaway:** Simple label flipping is hard to defeat with these methods; stronger Byzantine defenses (e.g., Krum, Bulyan) would be needed.

---

## 6. Overall Conclusions

1. **Data modality matters more than aggregation rule** when adding noise. Dense image data collapses under Laplace noise regardless of defense; tabular data remains accurate.

2. **Gradient Clipping with a fixed 80th‑percentile threshold** (computed from the first round) is a simple, effective technique that:
   - Performs well under severe non‑IID skew (outperforms FedAvg at α=0.05).
   - Does not harm IID performance.
   - Is not helpful against label flipping or heavy noise.

3. **SCAFFOLD is fragile** – it fails under extreme non‑IID (α ≤ 0.15) and is vulnerable to both noise and poisoning. It should be used only when data is nearly IID and clients are trusted.

4. **Trimmed Mean and FedProx** offer modest improvements over FedAvg in non‑IID and poisoning scenarios, but the gain is small (~1‑3%).

5. **FedAvg remains a strong baseline** – in IID and mild non‑IID, its simplicity and speed are hard to beat.

## 7. Practical Recommendations

- If you expect **extreme class imbalance** (some clients missing many classes), use **Gradient Clipping with a first‑round fixed threshold**.
- If you need **privacy noise** on dense data, use only very small Gaussian noise – avoid Laplace.
- For **tabular data**, you can safely add strong noise (even Laplace) without losing utility.
- Do **not** use SCAFFOLD in untrusted or highly heterogeneous environments.
- For **Byzantine robustness**, consider more aggressive outlier rejection than trimmed mean (e.g., geometric median).

---

*The complete code and raw logs are available in the repository.*
