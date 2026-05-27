# Federated Learning: Optimization, Security, and Literature Analysis

**Authors:** Sourit Mitra & Sagnik Chowdhury  
---

## Project Overview & Primary Objective
This repository encompasses our Algolabs internship project on Federated Learning (FL). The primary objective of this project was to fundamentally understand decentralized machine learning architectures, and then push the boundaries of the framework through practical experimentation. 

The project was executed in two main phases:
1. **Collaborative Foundation:** A joint literature study to understand the core mechanics and mathematics of standard [Federated Averaging (FedAvg)](https://arxiv.org/abs/1602.05629).
2. **Parallel Research Tracks:** We split into individual branches to tackle two of the biggest challenges in modern FL:
   * **Optimization & Heterogeneity (Sagnik):** Improving communication efficiency and model convergence when client data is highly Non-IID (unbalanced).
   * **Security & Robust Aggregation (Sourit):** Exposing model vulnerabilities (Inversion Attacks) and building robust, modality-aware privacy pipelines.

---

## Repository Structure & Navigation Guide
To keep our parallel experiments organized, this repository is structured across distinct branches. Please switch to the respective branches to view the full code, datasets, and detailed methodologies.

* 🌿 `main` *(You are here)*: Project overview and directory.
* 🌿 `lit-study`: Contains our collaborative notes and technical summary of the foundational FL paper.
* 🌿 `sagnik`: Contains experiments on communication efficiency, Dirichlet distributions, and FedProx using ResNet-18 on CIFAR-100.
* 🌿 `sourit`: Contains experiments on Model Inversion attacks and Defensive Aggregation (DP, Trimmed Mean, Clipping, SCAFFOLD) across varying data modalities.

---

## Phase 1: Collaborative Literature Study
*(Located in the `lit-study` branch)*

We began our project by analyzing the seminal 2017 paper that introduced the Federated Learning framework.
* **Paper Title:** [Communication-Efficient Learning of Deep Networks from Decentralized Data](https://arxiv.org/abs/1602.05629)
* **Authors:** H. Brendan McMahan, Eider Moore, Daniel Ramage, Seth Hampson, Blaise Agüera y Arcas
* **Output:** Our synthesized understanding of core concepts, communication challenges, and the FedAvg algorithm can be found in our [Literature Summary](https://github.com/Sagnik-Chowdhury/Federated-Learning-1/blob/lit-study/lit-study-summary.md)).

---

## Phase 2: Individual Research Tracks

### Track A: Optimization & Non-IID Distributions (Sagnik's Branch)
*(Please switch to the `sagnik` branch for full implementations)*

This track explores how model aggregation methods affect collaborative learning performance under highly heterogeneous (Non-IID) client distributions using a ResNet-18 CNN on the CIFAR-100 dataset.

**Key Experiments:**
1. **Standard vs. Delta Weight Aggregation:** Compared traditional full-weight sharing against a Delta strategy ($\Delta w = w_{local} - w_{global}$), drastically reducing redundant communication payloads by transmitting only the learned parameter changes.
2. **Dirichlet-Based Non-IID Partitioning:** Simulated realistic, heterogeneous client environments by partitioning the CIFAR-100 dataset using a Dirichlet distribution, heavily skewing the data available to local edge devices.
3. **FedProx Optimization:** Implemented the FedProx algorithm to combat the "client drift" caused by the Dirichlet partitioning. By introducing a proximal regularization term, the framework successfully stabilized optimization and achieved a best global accuracy of 22.47% after 25 communication rounds on the complex CIFAR-100 dataset.

### Track B: Privacy Vulnerabilities & Robust Defenses (Sourit's Branch)
*(Please switch to the `sourit` branch for full implementations)*

This track stress-tests the privacy guarantees of FL, proving that standard shared weights act as a mathematical memory of private client data. It then implements state-of-the-art defenses to evaluate the hypothesis that **data modality dictates privacy resilience**. 

**Key Experiments:**
1. **Model Inversion Attacks:** Executed Activation Maximization attacks to reverse-engineer frozen model weights. We successfully extracted spatial features (ghostly pixels) from an Image Network (MNIST) and archetypal statistical thresholds from a Tabular Network (Breast Cancer).
2. **Robust Aggregation Pipelines:** Replaced standard FedAvg with three advanced algorithms to protect the server from data poisoning and client drift:
   * **Trimmed Mean (Quantile) Aggregation**
   * **Gradient Clipping (L2 Norm Limits)**
   * **FedProx & SCAFFOLD (Control Variates & Drift Mitigation)**
3. **Differential Privacy (DP) vs. Modality:** Injected Gaussian and heavy-tailed Laplace noise into the aggregated models. The experiments conclusively proved that dense spatial data (Images) suffers catastrophic utility loss under Laplace noise, whereas scattered/independent data (Tabular features) comfortably absorbs aggressive noise with near-zero accuracy drops (>93% retention).
