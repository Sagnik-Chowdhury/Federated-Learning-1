# Federated Learning: Privacy Attacks & Modality‑Aware Robust Aggregation


This branch contains my individual research on **security vulnerabilities and robust aggregation strategies** in Federated Learning. The work is divided into two major thrusts:

1. **Model Inversion Attacks** – demonstrating that shared model weights leak private client data.
2. **Defensive Aggregation** – implementing and stress‑testing five algorithms against noise, data skew, and poisoning, with a focus on how **data modality** (dense images vs scattered tabular features) affects resilience.

All experiments are fully reproducible. Each notebook contains the complete pipeline: data loading, client partitioning, federated rounds, aggregation, and evaluation.

---

## Individual Research & Vulnerability Experiments

Following our collaborative [literature review](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/blob/lit-study/lit-study-summary.md), I branched into practical experiments to stress-test the framework's privacy guarantees.

### Model Inversion across Data Modalities

This experiment demonstrates a major vulnerability in standard Federated Learning: because shared model weights act as a mathematical memory of the client data, an adversary can reverse-engineer those weights to reconstruct properties of the private training set.

To test how **data modality** impacts privacy, I executed this attack against two distinct data structures:

- **Integrated/Dense Data:** High-dimensional images (MNIST).
- **Scattered/Tabular Data:** Low-dimensional, continuous features (Breast Cancer Dataset).

#### Architecture & Methodology

- **Network Topologies:**
  - *Image Model:* A 4-layer MLP mapping 784 input pixels down to 10 class logits.
  - *Tabular Model:* A 3-layer MLP mapping 30 standardized medical features to 2 diagnosis classes.

- **Attack Method:** Optimization-based Model Inversion (Activation Maximization). The trained weights of the network are completely frozen on the server side. Pure random noise is fed as a dummy input, and gradient descent is used to optimize the input values to maximize the network's confidence scores for specific target classes.

#### Implementation Files

The implementation is split into two sequential Jupyter Notebooks:

- **`[Model Construction](Model_construction)`** – Handles the pipeline setup, constructs the neural networks for both datasets, performs local training, and serializes the resulting client weights (`fedavg_mnist_weights.pth` and `fedavg_tabular_weights.pth`).
- **[`Model Inversion`](Model_Inversion.ipynb)** – Loads the frozen target architectures and saved weights, then executes the inversion loop. It dynamically visualizes the ghostly reconstructed pixels for the MNIST dataset, and generates archetypal feature‑importance bar charts for the tabular dataset.

---

## Robust Aggregation & Defenses

After confirming the inversion vulnerability, I implemented and evaluated five aggregation strategies under three stress conditions: statistical noise, non‑IID data skew, and label‑flipping attacks.

### 1. Noise Robustness – Dense vs Tabular Data

**Setup:** 20 clients, IID split, 5 rounds. Server injects Gaussian or Laplace noise (scale = 0.05) after aggregation.

#### FedAvg (Baseline)
- **MNIST:** No noise → 94.6%; Gaussian → 86.9%; Laplace → 66.7%
- **Tabular:** No noise → 99.1%; Gaussian → 98.2%; Laplace → 96.5%

#### Gradient Clipping (fixed 80th‑percentile threshold)
- **MNIST:** No noise → 92.2%; Gaussian → 30.8%; Laplace → 10.6%
- **Tabular:** No noise → 95.6%; Gaussian → 93.0%; Laplace → 94.7%

#### Trimmed Mean (trim 10%)
- **MNIST:** No noise → 92.4%; Gaussian → 80.9%; Laplace → 59.0%
- **Tabular:** No noise → 96.5%; Gaussian → 96.5%; Laplace → 94.7%

#### FedProx (μ = 0.01)
- **MNIST:** No noise → 92.5%; Gaussian → 77.4%; Laplace → 25.9%
- **Tabular:** No noise → 97.4%; Gaussian → 96.5%; Laplace → 96.5%

#### SCAFFOLD
- **MNIST:** No noise → 26.2%; Gaussian → 7.8%; Laplace → 6.5%
- **Tabular:** No noise → 62.3%; Gaussian → 63.2%; Laplace → 66.7%

**Conclusion:** Dense image data is extremely fragile under Laplace noise. Tabular data remains robust regardless of noise type.

---

### 2. IID Baseline – CIFAR‑10

**Setup:** 10 clients, IID split, 25 rounds, CNN model.

- **FedAvg:** 70.4%
- **Trimmed Mean:** 69.8%
- **Gradient Clipping:** 67.9%
- **FedProx:** 66.4%
- **SCAFFOLD:** 69.3%

FedAvg is optimal in IID settings; others are close but not better.

---

### 3. Non‑IID Skew – Dirichlet α (0.05 to 0.45)

**Key results at α = 0.05 (extreme skew):**
- **FedAvg:** 47.4%
- **Trimmed Mean:** 46.3%
- **Gradient Clipping:** 51.6% (best)
- **FedProx:** 46.7%
- **SCAFFOLD:** 10.0% (diverged, loss NaN)

As α increases (more balanced), all methods converge toward the IID baseline. Gradient Clipping with the fixed 80th‑percentile threshold consistently outperforms FedAvg at high skew.

---

### 4. Label Flipping Attack (20% malicious)

**Setup:** 10 clients, IID split, 2 malicious clients flip labels `label = (label + 5) % 10`, 10 rounds.

- **FedAvg:** 54.2%
- **Trimmed Mean:** 55.5%
- **Gradient Clipping:** 54.1%
- **FedProx:** 55.3%
- **SCAFFOLD:** 51.6%

All methods drop ~15% from clean IID. Trimmed Mean and FedProx offer marginal (+1%) improvement. SCAFFOLD is worst.

---

## Practical Recommendations

- **For privacy noise on dense data:** Use only very small Gaussian noise. Avoid Laplace.
- **For tabular data:** Aggressive noise (even Laplace) can be added with minimal utility loss.
- **For extreme non‑IID:** Use **Gradient Clipping with a fixed 80th‑percentile threshold** (computed from the first round). Simple and effective.
- **Do NOT use SCAFFOLD** in highly heterogeneous or untrusted environments.
- **For label flipping attacks,** consider stronger Byzantine defenses (e.g., Krum, Bulyan).

---

## How to Run

1. Clone the repository and switch to the `Sourit` branch:
   ```bash
   git clone https://github.com/Sagnik-Chowdhury/Federated-Learning-1.git
   cd Federated-Learning-1
   git checkout Sourit
