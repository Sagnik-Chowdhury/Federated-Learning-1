# Empirical Study: Federated Aggregation & Noise Resilience

As part of our Algolabs project, we moved beyond theoretical literature to conduct rigorous empirical simulations. Our objective was to benchmark advanced aggregation strategies against diverse network environments and data modalities.

We tested five distinct algorithms: **FedAvg** (Baseline), **Trimmed Mean**, **Absolute Gradient Clipping**, **FedProx**, and **SCAFFOLD**.

---

## Part 1: Network Robustness & Adversarial Conditions
We evaluated the algorithms against three distinct network states to observe how they handle optimal distribution, adversarial attacks, and extreme statistical drift.

### 1. IID (Perfectly Distributed Data)
The control environment. Data is perfectly balanced across all clients.
| Strategy | Final Accuracy (%) |
| :--- | :--- |
| **Trimmed Mean** | **59.03** |
| FedProx | 58.60 |
| FedAvg | 58.31 |
| Gradient Clipping | 34.22 |
| SCAFFOLD | 10.00 *(Collapsed)* |

### 2. Data Poisoning (20% Malicious Clients)
Simulating an adversarial attack where a minority of clients intentionally submit corrupted gradients (Label Flipping).
| Strategy | Final Accuracy (%) |
| :--- | :--- |
| **FedAvg** | **55.30** |
| Trimmed Mean | 55.12 |
| FedProx | 54.09 |
| SCAFFOLD | 53.77 |
| Gradient Clipping | 28.82 |

### 3. Non-IID (Extreme Client Drift)
Using a Dirichlet Distribution ($\alpha = 0.1$) to create severe statistical skew, representing real-world isolated data silos.
| Strategy | Final Accuracy (%) |
| :--- | :--- |
| **FedAvg** | **43.47** |
| FedProx | 42.91 |
| Trimmed Mean | 39.14 |
| SCAFFOLD | 22.57 |
| Gradient Clipping | 10.00 *(Collapsed)* |

**Section Conclusion:** There is no "silver bullet" algorithm. While **Trimmed Mean** excels in perfectly distributed environments, it struggles with Non-IID data because it mistakenly filters out valid, highly-skewed local classes as "outliers." Conversely, mathematically complex strategies like **SCAFFOLD** and **Absolute Gradient Clipping** exhibit severe hyper-parameter sensitivity, frequently leading to gradient explosion or total model collapse if not meticulously tuned. The standard **FedAvg** remains surprisingly resilient across all scenarios.

---

## Part 2: Data Modality & Statistical Noise Injection
To evaluate privacy preservation, we injected statistical noise into the server's aggregated weights. We compared **Gaussian (Normal) Noise** against heavy-tailed **Laplace Noise**, testing them across two fundamentally different data architectures:
* **Dense Data (MNIST):** High-dimensional, structured pixel data.
* **Scattered Data (Breast Cancer Tabular):** Low-dimensional, independent features.

### Strategy Benchmarks Under Noise

**1. Standard FedAvg**
| Dataset Modality | Baseline (No Noise) | Normal (Gaussian) | Laplace Noise |
| :--- | :--- | :--- | :--- |
| **MNIST (Dense)** | 94.64% | 86.89% | 66.65% |
| **Tabular (Scattered)** | 99.12% | 98.24% | 96.49% |

**2. Trimmed Mean**
| Dataset Modality | Baseline (No Noise) | Normal (Gaussian) | Laplace Noise |
| :--- | :--- | :--- | :--- |
| **MNIST (Dense)** | 92.00% | 76.00% | 46.00% |
| **Tabular (Scattered)** | 96.00% | 94.00% | 94.00% |

**3. Absolute Gradient Clipping**
| Dataset Modality | Baseline (No Noise) | Normal (Gaussian) | Laplace Noise |
| :--- | :--- | :--- | :--- |
| **MNIST (Dense)** | 75.09% | 7.82% *(Collapse)* | 7.35% *(Collapse)* |
| **Tabular (Scattered)** | 97.36% | 94.73% | 76.31% |

**4. FedProx**
| Dataset Modality | Baseline (No Noise) | Normal (Gaussian) | Laplace Noise |
| :--- | :--- | :--- | :--- |
| **MNIST (Dense)** | 92.45% | 77.35% | 25.94% |
| **Tabular (Scattered)** | 97.36% | 96.49% | 96.49% |

**5. SCAFFOLD**
| Dataset Modality | Baseline (No Noise) | Normal (Gaussian) | Laplace Noise |
| :--- | :--- | :--- | :--- |
| **MNIST (Dense)** | 26.20% | 7.82% *(Collapse)* | 6.49% *(Collapse)* |
| **Tabular (Scattered)** | 62.28% | 63.15% | 66.66% |

### Section Conclusion: The Modality Barrier

Our empirical tests reveal a fundamental divergence in how different data structures process statistical noise, independent of the aggregation strategy used.

**The Fragility of Dense Pixel Data:** Across all algorithms, dense image networks rely on fragile spatial correlations that are easily destroyed by extreme perturbations. While Gaussian noise causes moderate accuracy degradation, heavy-tailed Laplace noise proves catastrophic, repeatedly shattering MNIST accuracy regardless of the stabilizing algorithm used. 

**The Resilience of Scattered Features:** In stark contrast, tabular datasets demonstrate incredible structural robustness. Because tabular features are largely independent, heavy-tailed Laplace perturbations act as a mild regularizer rather than a destructive force. Algorithms like **FedProx** combined with tabular data managed to sustain near-perfect baseline accuracies (96.49%) even under heavy noise.

**Project Milestone:** This study validates the "No Free Lunch Theorem" in Federated Learning. The choice of algorithmic defense and statistical noise must be strictly paired to both the network environment and the underlying data modality.
