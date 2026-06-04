# Federated Learning: Optimization, Security, and Literature Analysis

**Authors:** Sourit Mitra & Sagnik Chowdhury  

---

## Project Overview & Primary Objective
This repository encompasses our Algolabs internship project on Federated Learning (FL). The primary objective of this project was to fundamentally understand decentralized machine learning architectures, and then push the boundaries of the framework through practical experimentation. 

The project was executed in two main phases:
1. **Collaborative Foundation:** A joint literature study to understand the core mechanics and mathematics of standard [Federated Averaging (FedAvg)](https://arxiv.org/abs/1602.05629) and the challenges of decentralized learning.
2. **Parallel research tracks** – each focusing on a major FL challenge:
   - **Sagnik:** Optimization & heterogeneity (Non‑IID data, communication efficiency)
   - **Sourit:** Security, privacy attacks, and robust aggregation

---

## Repository Structure & Navigation Guide
To keep our parallel experiments organized, this repository is structured across distinct branches. Please switch to the respective branches to view the full code, datasets, and detailed methodologies.

* 🌿 `main` *(You are here)*: Project overview and directory.
* 🌿 [`lit-study`](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/tree/lit-study): Contains our **collaborative literature summary** of the foundational FL paper, as well as our **joint empirical study report** that synthesises results from both individual branches.
* 🌿 [`Sagnik`](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/tree/Sagnik): Contains experiments on communication efficiency, Dirichlet distributions, and FedProx using ResNet-18 on CIFAR-100.
* 🌿 [`Sourit`](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/tree/Sourit): Contains extensive experiments on Model Inversion attacks, Defensive Aggregation (Trimmed Mean, Gradient Clipping, FedProx, SCAFFOLD), and modality‑aware privacy noise injection.

---

## Phase 1: Collaborative Literature & Empirical Study
*(Located in the [`lit-study`](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/tree/lit-study) branch)*

We began our project by analyzing the seminal 2017 paper that introduced the Federated Learning framework.
* **Paper Title:** [Communication-Efficient Learning of Deep Networks from Decentralized Data](https://arxiv.org/abs/1602.05629)
* **Authors:** H. Brendan McMahan, Eider Moore, Daniel Ramage, Seth Hampson, Blaise Agüera y Arcas
* **Literature Output:** Our synthesized understanding of core concepts, communication challenges, and the FedAvg algorithm can be found in our [Literature Summary](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/blob/lit-study/lit-study-summary.md).

After completing our individual experiments, we jointly authored a **comprehensive empirical study report** that compares and contrasts the results from both research tracks. This report is available in the same branch:  
[**Empirical Study Report**](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/blob/lit-study/empirical-study-results.md)

---

## Phase 2: Individual Research Tracks

### Track A: Optimization & Non-IID Distributions (Sagnik's Branch)
*(Please switch to the [`Sagnik`](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/tree/Sagnik) branch for full implementations)*

This track explores how model aggregation methods affect collaborative learning performance under highly heterogeneous (Non-IID) client distributions using a ResNet-18 CNN on the CIFAR-100 dataset.

**Key Experiments:**
1. **[Standard](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/blob/Sagnik/FL_CIFAR_100_ResNet_18.ipynb) vs. [Delta Weight Aggregation](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/blob/Sagnik/FL_CIFAR_100_ResNet_18_Delta%20.ipynb):** Compared traditional full-weight sharing against a Delta strategy (Δw = w_local − w_global), drastically reducing redundant communication payloads by transmitting only the learned parameter changes.
2. **[Dirichlet-Based Non-IID Partitioning](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/blob/Sagnik/FL_CIFAR_100_ResNet_18_Dirichlet.ipynb):** Simulated realistic, heterogeneous client environments by partitioning the CIFAR-100 dataset using a Dirichlet distribution, heavily skewing the data available to local edge devices.
3. **[FedProx Optimization](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/blob/Sagnik/FL_CIFAR_100_ResNet_18_FedProx.ipynb):** Implemented the FedProx algorithm to combat the "client drift" caused by the Dirichlet partitioning. By introducing a proximal regularization term, the framework successfully stabilized optimization and achieved a best global accuracy of 22.47% after 25 communication rounds on the complex CIFAR-100 dataset.

### Track B: Privacy Vulnerabilities & Robust Defenses (Sourit's Branch)
*(Please switch to the [`Sourit`](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/tree/Sourit) branch for full implementations)*

This track covers two main themes: **exposing vulnerabilities** (Model Inversion) and **building defenses** (Trimmed Mean, Gradient Clipping, FedProx, SCAFFOLD) tested under noise, non‑IID, and poisoning.

#### Model Inversion Attack (Two‑notebook pipeline)

- **[`Model Construction`](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/blob/Sourit/Model_Construction.ipynb))** 
  Constructs two neural networks: a 4‑layer MLP for MNIST (784→256→128→64→10) and a 3‑layer MLP with dropout for the Breast Cancer dataset (30→16→8→2). Trains them in a federated setting (20 clients, IID split) and saves the resulting weights.

- **[`Model Inversion`](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/blob/Sourit/Model_Inversion.ipynb)**  
  Loads the frozen weights and performs **Activation Maximisation** (optimising random noise to maximise the network’s confidence for a target class). Successfully reconstructs ghostly digit images from MNIST and archetypal feature importance charts for Breast Cancer, proving that shared weights leak private information.

#### Robust Aggregation Defenses

All defense notebooks run on both MNIST and Breast Cancer, with and without server‑side noise (Gaussian / Laplace).

1. **[`FedAvg`](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/blob/Sourit/FedAvg.ipynb)**  
   Standard weighted averaging of client models. Used as the baseline for all experiments.
   *Result:* Dense image data suffers a large drop under Laplace noise, while tabular data remains almost unaffected – establishing the modality bias that persists across all defenses.

1. **[`Trimmed Mean`](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/blob/Sourit/Trimmed_Mean.ipynb)**  
   Discards top and bottom 10% of client parameter updates per coordinate before averaging.  
   *Result:* Protects tabular data well; dense data still fragile under Laplace noise.

2. **[`Gradient Clipping`](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/blob/Sourit/Gradient_Clipping.ipynb)**  
   Introduces a **fixed clipping threshold** derived from the **80th percentile** of client weight L2 norms in the **first round**. Reuses the same threshold for all future rounds.  
   *Result:* Extremely effective under severe non‑IID (outperforms FedAvg at α=0.05), but does not protect dense data from Laplace noise.

3. **[`Fedprox`](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/blob/Sourit/Fedprox.ipynb)**  
   Adds a proximal penalty (μ = 0.01) to local training.  
   *Result:* Helps recover from Gaussian noise on MNIST (77% vs 86% baseline) but unstable under Laplace.

4. **[`Scaffolding`](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/blob/Sourit/Scaffolding.ipynb)**  
   Implements control variates for drift correction.  
   *Result:* Fails on dense MNIST (26% even without noise) and diverges under extreme non‑IID. Not recommended for heterogeneous settings.

#### [`IID Experiment`](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/blob/Sourit/IID_FL.ipynb)

Benchmarks all five strategies on CIFAR‑10 with **perfect IID split** (10 clients, 25 rounds).  
- FedAvg reaches 70.4% accuracy; others are within 1‑4% lower.  
Establishes the ideal ceiling for each method.

#### [`Non-IID Experiment`](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/blob/Sourit/Non_IID_FL.ipynb)

Partitions CIFAR‑10 with Dirichlet α = 0.05, 0.15, 0.25, 0.35, 0.45 (α smaller = more skew). Runs 25 rounds for all five strategies.  
- **Gradient Clipping (fixed 80th percentile) beats FedAvg** at α=0.05: 51.6% vs 47.4%.  
- SCAFFOLD collapses (accuracy → 10%, loss NaN) at α ≤ 0.15.  
- As α increases, all methods converge toward the IID baseline.

#### [`Data Poisoning`](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/blob/Sourit/Byzantine_Robustness.ipynb)

Simulates a Byzantine attack: 2 out of 10 clients flip labels (`new_label = (original_label + 5) % 10`) for every batch. Runs 10 rounds with IID data (to isolate poisoning effect).  
- All methods drop from ~70% to ~55% accuracy.  
- Trimmed Mean and FedProx give marginal +1% improvement over FedAvg.  
- SCAFFOLD performs worst (51.6%).

---

## Final Joint Findings

- **Data modality dominates noise resilience:** Dense image data collapses under Laplace noise; tabular data stays robust regardless of defense.
- **Gradient Clipping with a fixed 80th‑percentile threshold** (first‑round derived) is simple, parameter‑free, and outperforms FedAvg under extreme non‑IID.
- **SCAFFOLD is not robust** – it fails under heterogeneity and poisoning.
- **Label flipping** is a hard attack; stronger Byzantine methods (e.g., Krum, Bulyan) are needed for higher compromise rates.
- **FedAvg remains optimal** in IID and mild non‑IID settings.
