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

This branch contains my complete experimental work on attacking and defending Federated Learning systems. The full details, including all results and analysis, are documented in the [Sourit Branch README](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/blob/Sourit/README.md).

**Highlights:**
- **Model Inversion Attacks** – reconstructing private training data from shared weights.
- **Five robust aggregation strategies** tested under noise, non‑IID skew, and label flipping.
- **Key discovery:** Dense image data is highly fragile under Laplace noise; tabular data remains robust.

---

## Final Joint Findings
- **Data modality is the dominant factor** in privacy‑noise robustness. Dense data collapses; tabular data survives.
- **Gradient Clipping with a fixed 80th‑percentile threshold** (computed from the first round) is simple and effective under extreme non‑IID, outperforming FedAvg at α=0.05.
- **SCAFFOLD fails** under severe heterogeneity and is not recommended for real‑world deployments.
- No tested defense fully recovers from label flipping attacks – stronger Byzantine‑robust methods are needed.

For complete code, logs, and visualisations, please explore the respective branches.
