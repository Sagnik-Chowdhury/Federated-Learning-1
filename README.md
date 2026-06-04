# Federated Learning: Literature Study & Empirical Simulation

**Authors:** Sourit Mitra & Sagnik Chowdhury  

## Project Overview
This repository contains our collaborative literature study and technical summary of the seminal 2017 paper on decentralized machine learning architectures. This review was conducted as part of our project work at Algolabs. 

Following the literature review, we expanded our scope to include **practical, empirical simulations**. We built a custom testing suite to evaluate how various Federated Learning aggregation algorithms behave under extreme network conditions (Non-IID data, malicious actors) and varying statistical noise constraints.

## Source Material
* **Paper Title:** [Communication-Efficient Learning of Deep Networks from Decentralized Data](https://arxiv.org/abs/1602.05629)
* **Authors:** H. Brendan McMahan, Eider Moore, Daniel Ramage, Seth Hampson, Blaise Agüera y Arcas 

## Documentation & Reports

**1. [Theoretical Foundation]((lit-study-summary.md))**
Our complete, synthesized understanding of the paper's core concepts, challenges, and the FederatedAveraging (FedAvg) algorithm can be found in our study document:


**2. [Empirical Experiments](empirical-study-results.md)**
Our practical testing results, benchmarking 5 different aggregation strategies (FedAvg, Trimmed Mean, Gradient Clipping, FedProx, SCAFFOLD) across diverse data modalities and adversarial network setups:

- **Noise robustness** – dense vs tabular data under Gaussian/Laplace noise.
- **IID baseline** – CIFAR‑10 with uniform split.
- **Non‑IID skew** – Dirichlet partitions (α = 0.05 … 0.45).
- **Data poisoning** – label flipping attack (20% malicious).

