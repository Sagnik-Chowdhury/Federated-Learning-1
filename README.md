# Federated Learning: Literature Study & Security Analysis

**Authors:** Sourit Mitra (MDS202537), Sagnik Chowdhury, Swikriti Paul  
**Under the supervision of:** Priyavrat C. Deshpande and Saipriya Dubey

## Project Overview
This repository contains our collaborative literature study and technical summary of the seminal paper on decentralized machine learning architectures. This review and subsequent vulnerability research was conducted as part of our project work at Algolabs. 

## Source Material
* **Paper Title:** [Communication-Efficient Learning of Deep Networks from Decentralized Data](https://arxiv.org/abs/1602.05629)
* **Authors:** H. Brendan McMahan, Eider Moore, Daniel Ramage, Seth Hampson, Blaise Agüera y Arcas 

## Study Notes
Our complete, synthesized understanding of the paper's core concepts, challenges, and the FederatedAveraging (FedAvg) algorithm can be found in our study document:
* [Read the Literature Summary Here](lit-study-summary.md)

---

## Individual Research & Vulnerability Experiments

Following our collaborative literature review, we branched into practical experiments to stress-test the framework's privacy guarantees. 

### Model Inversion across Data Modalities
This experiment demonstrates a major vulnerability in standard Federated Learning: because shared model weights act as a mathematical memory of the client data, an adversary can reverse-engineer those weights to reconstruct properties of the private training set. 

To test how **data modality** impacts privacy, we executed this attack against two distinct data structures:
1. **Integrated/Dense Data:** High-dimensional images (MNIST).
2. **Scattered/Tabular Data:** Low-dimensional, continuous features (Breast Cancer Dataset).

#### Architecture & Methodology
* **Network Topologies:** * *Image Model:* A 4-layer MLP mapping 784 input pixels down to 10 class logits.
  * *Tabular Model:* A 3-layer MLP mapping 30 standardized medical features to 2 diagnosis classes.
* **Attack Method:** Optimization-based Model Inversion (Activation Maximization). The trained weights of the network are completely frozen on the server side. Pure random noise is fed as a dummy input, and gradient descent is used to optimize the input values to maximize the network's confidence scores for specific target classes.

#### Implementation Files
The implementation is split into two sequential Jupyter Notebooks:
1. **[Internship_Fed_1.ipynb](Internship_Fed_1.ipynb):** Handles the pipeline setup, local training for both datasets, and serialization of the resulting client weights (`fedavg_mnist_weights.pth` and `fedavg_tabular_weights.pth`).
2. **[Internship_Fed_2.ipynb](Internship_Fed_2.ipynb):** Loads the frozen target architectures and saved weights, executing the inversion loop. It dynamically visualizes the ghostly reconstructed pixels for the MNIST dataset, and generates archetypal feature-importance bar charts for the tabular dataset.

---

## Robust Aggregation & Differential Privacy (DP) Defenses

Following the successful reconstruction attacks, we shifted focus to implementing and stress-testing industry-standard defense mechanisms, evaluating how our two data modalities responded to advanced privacy filters.

### 1. Trimmed Mean (Quantile) Aggregation
Standard Federated Averaging is highly vulnerable to data poisoning from malicious or skewed clients. To mitigate this, we implemented a Trimmed Mean aggregation strategy at the server level.
* **Mechanism:** The server sorts all client parameter updates and discards the extreme outliers (the top 5% and bottom 5% of updates). The remaining 90% is averaged to form a safe, robust global model.
* **[Trimmed Mean Aggregation](Fed_Trimmed_Mean_Aggregation.ipynb)**

### 2. Gradient Clipping & DP-SGD
As an alternative to quantile filtering, we implemented a strict mathematical constraint on client updates, forming a standard Differential Privacy pipeline.
* **Mechanism:** The central server calculates the magnitude (L2 norm) of each client's proposed update. If the update exceeds a rigid threshold, it is mathematically scaled down. 
* **[Gradient Clipping](Fed_Gradient_Clipping.ipynb)**

### 3. Mitigating Client Drift with FedProx
To address highly heterogeneous (non-IID) client data, we modified the local training loop using the FedProx algorithm.
* **Mechanism:** Clients add a Proximal Penalty to their standard loss function. This mathematically anchors the local updates, forcing clients to learn from their local data without straying too far from the global model's state. 
* **[FedProx](FedProx.ipynb)**

### 4. Advanced Drift Correction with SCAFFOLD
To push client drift mitigation to the mathematical limit, we implemented Stochastic Controlled Averaging (SCAFFOLD).
* **Mechanism:** Instead of a loss penalty, SCAFFOLD uses Control Variates. The server tracks global update trajectories, and clients track local data biases, mathematically correcting their gradients ($g = g - c_i + c$) during the optimization step to maintain alignment with the global objective.
* **[SCAFFOLD](Scaffolding.ipynb)**

---

### Final Project Findings: Data Modality vs. Differential Privacy
In all four defense notebooks, after securing the aggregation step, the central server injected statistical noise to mask individual client contributions. We compared standard Gaussian noise against heavy-tailed Laplace noise. 

The experiments yielded conclusive, repeatable evidence across all architectures:
1. **Integrated Data is Fragile:** Dense image networks (MNIST) suffered catastrophic forgetting under heavy-tailed noise. Across all defense strategies, injecting Laplace noise plummeted the image model's accuracy to unusable levels.
2. **Scattered Data is Robust:** The tabular networks (Breast Cancer features) showed incredible resilience. They comfortably absorbed aggressive Laplace noise, maintaining exceptional accuracy and stability throughout the testing phases.

**Conclusion:** Robust aggregation (Trimmed Mean, Clipping, FedProx, SCAFFOLD) effectively protects against data poisoning and client drift. However, statistical privacy mechanisms must be uniquely tailored to the data's underlying structure. Dense spatial data cannot survive Laplace distributions, whereas scattered tabular data requires it for true differential privacy without utility loss.
