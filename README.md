# Federated Learning: Literature Study & Empirical Simulation

**Authors:** Sourit Mitra & Sagnik Chowdhury  

## Project Overview
This repository contains our collaborative literature study and technical summary of the seminal 2017 paper on decentralized machine learning architectures. This review was conducted as part of our project work at Algolabs. 

Following the literature review, we expanded our scope to include **practical, empirical simulations**. We built a custom testing suite to evaluate how various Federated Learning aggregation algorithms behave under extreme network conditions (Non-IID data, malicious actors) and varying statistical noise constraints.

## Source Material
* **Paper Title:** [Communication-Efficient Learning of Deep Networks from Decentralized Data](https://arxiv.org/abs/1602.05629)
* **Authors:** H. Brendan McMahan, Eider Moore, Daniel Ramage, Seth Hampson, Blaise Agüera y Arcas 

## Documentation & Reports

**1. Theoretical Foundation**
Our complete, synthesized understanding of the paper's core concepts, challenges, and the FederatedAveraging (FedAvg) algorithm can be found in our study document:
* 📖 [Read the Literature Summary Here](lit-study-summary.md)

**2. Empirical Experiments**
Our practical testing results, benchmarking 5 different aggregation strategies (FedAvg, Trimmed Mean, Gradient Clipping, FedProx, SCAFFOLD) across diverse data modalities and adversarial network setups:
* 📊 [Read the Empirical Study Results Here](empirical-study-results.md)
