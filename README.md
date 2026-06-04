# Federated Learning: Privacy Attacks & Modality‑Aware Robust Aggregation

**Author:** Sourit Mitra  
**Branch:** `Sourit`

This branch contains my individual research on **security vulnerabilities and robust aggregation strategies** in Federated Learning. The work is divided into two major thrusts:

1. **Model Inversion Attacks** – demonstrating that shared model weights leak private client data.
2. **Defensive Aggregation** – implementing and stress‑testing five algorithms against noise, data skew, and poisoning, with a focus on how **data modality** (dense images vs scattered tabular features) affects resilience.

All experiments are fully reproducible. Each notebook contains the complete pipeline: data loading, client partitioning, federated rounds, aggregation, and evaluation.

---

## Repository Contents

| Notebook | Description | Key Results |
|----------|-------------|-------------|
| `Fed_Model_Inversion_MNIST_Tabular.ipynb` | Activation Maximisation attack on frozen FL models. Reconstructs private inputs from shared weights. | Successfully extracted ghostly MNIST digits and archetypal breast cancer features. |
| `Fed_Trimmed_Mean_Aggregation.ipynb` | Trimmed Mean (discard top/bottom 10% of updates) with Gaussian/Laplace noise. | Tabular data retains 96% accuracy under Laplace; MNIST drops to 59%. |
| `Fed_Gradient_Clipping.ipynb` | Fixed 80th‑percentile threshold clipping (first round) + noise injection. | MNIST with Laplace → 10.6%; Tabular with Laplace → 94.7%. |
| `Fedprox.ipynb` | FedProx (μ=0.01) with proximal penalty + noise. | MNIST noise‑recovery oscillates; tabular stays near 96%. |
| `Scaffolding.ipynb` | SCAFFOLD control variates + noise. | Collapses on MNIST (26% even without noise); weak on tabular (62%). |
| `IID_Experiment_CIFAR10.ipynb` | Benchmarks all 5 strategies on IID CIFAR‑10 (25 rounds). | FedAvg best (70.4%), others within 1‑4%. |
| `NonIID_Experiment_CIFAR10.ipynb` | Dirichlet partitions (α=0.05,0.15,0.25,0.35,0.45). 25 rounds. | Gradient clipping outperforms FedAvg at α=0.05 (51.6% vs 47.4%); SCAFFOLD diverges. |
| `Data_Poisoning_Label_Flipping.ipynb` | 2/10 malicious clients flip labels (+5 mod 10). 10 rounds. | All methods drop to ~55%; Trimmed Mean and FedProx give marginal +1%. |

---

## Key Experimental Findings

### 1. Model Inversion Attack
- **Attack success:** By optimising random noise against a frozen model, the adversary can reconstruct visual patterns (MNIST) and statistical thresholds (Breast Cancer).
- **Implication:** Standard FedAvg without defences leaks private information. Defences (clipping, noise) are necessary.

### 2. Statistical Noise & Data Modality
| Strategy | Dataset | No Noise | Gaussian | Laplace |
|----------|---------|----------|----------|---------|
| FedAvg | MNIST | 94.6% | 86.9% | 66.7% |
| FedAvg | Tabular | 99.1% | 98.2% | 96.5% |
| Gradient Clipping | MNIST | 92.2% | 30.8% | 10.6% |
| Gradient Clipping | Tabular | 95.6% | 93.0% | 94.7% |
| Trimmed Mean | MNIST | 92.4% | 80.9% | 59.0% |
| Trimmed Mean | Tabular | 96.5% | 96.5% | 94.7% |
| FedProx | MNIST | 92.5% | 77.4% | 25.9% |
| FedProx | Tabular | 97.4% | 96.5% | 96.5% |
| SCAFFOLD | MNIST | 26.2% | 7.8% | 6.5% |
| SCAFFOLD | Tabular | 62.3% | 63.2% | 66.7% |

**Conclusion:** Dense image data is extremely fragile under heavy‑tailed (Laplace) noise. Tabular data is remarkably robust. No defence fixes this – modality is the dominant factor.

### 3. IID Baseline (CIFAR‑10, 10 clients, 25 rounds)
| Strategy | Final Accuracy |
|----------|----------------|
| FedAvg | 70.4% |
| Trimmed Mean | 69.8% |
| Gradient Clipping | 67.9% |
| FedProx | 66.4% |
| SCAFFOLD | 69.3% |

**Conclusion:** FedAvg is optimal in IID settings. Others are close but not better.

### 4. Non‑IID Skew (Dirichlet α)
- **Gradient Clipping with fixed 80th‑percentile threshold** outperforms FedAvg at α=0.05 (51.6% vs 47.4%).
- **SCAFFOLD collapses** at α ≤ 0.15 (accuracy → 10%, loss → NaN).
- As α increases, all converge toward IID performance.

### 5. Label Flipping Attack (20% malicious)
| Strategy | Final Accuracy |
|----------|----------------|
| FedAvg | 54.2% |
| Trimmed Mean | 55.5% |
| Gradient Clipping | 54.1% |
| FedProx | 55.3% |
| SCAFFOLD | 51.6% |

**Conclusion:** All methods drop ~15% from clean IID. Trimmed Mean and FedProx offer marginal gains (+1%). SCAFFOLD is worst.

---

## Practical Recommendations

1. **For privacy noise on dense data:** Use only very small Gaussian noise. Avoid Laplace.
2. **For tabular data:** You can add aggressive noise (even Laplace) with minimal utility loss.
3. **For extreme non‑IID:** Use **Gradient Clipping with a fixed 80th‑percentile threshold** (computed from the first round). It is simple and outperforms more complex methods.
4. **Do NOT use SCAFFOLD** in highly heterogeneous or untrusted environments.
5. **Label flipping is hard to defeat** with these methods. Consider stronger Byzantine defenses (Krum, Bulyan) if poisoning is a major threat.

---

## How to Run

1. Clone the repository and switch to the `Sourit` branch:
   ```bash
   git clone https://github.com/Sagnik-Chowdhury/Federated-Learning-1.git
   cd Federated-Learning-1
   git checkout Sourit
