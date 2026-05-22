# Federated Learning: Literature Study

**Authors:** Sourit Mitra & Sagnik Chowdhury  

## Project Overview
This repository contains our collaborative literature study and technical summary of the seminal paper on decentralized machine learning architectures. This review was conducted as part of our project work at Algolabs. 

## Source Material
* **Paper Title:** [Communication-Efficient Learning of Deep Networks from Decentralized Data](https://arxiv.org/abs/1602.05629)
* **Authors:** H. Brendan McMahan, Eider Moore, Daniel Ramage, Seth Hampson, Blaise Agüera y Arcas 

## Study Notes
Our complete, synthesized understanding of the paper's core concepts, challenges, and the FederatedAveraging (FedAvg) algorithm can be found in our study document:
* [Read the Literature Summary Here](lit-study-summary.md)

## Part 2: Individual Research & Vulnerability Experiments

Following our collaborative literature review on Federated Learning architectures, we branched into individual practical experiments to stress-test the framework's privacy guarantees. 

### Sourit's Experiment: Model Inversion & Feature Reconstruction
This experiment demonstrates a major vulnerability in standard Federated Learning: because shared model weights act as a mathematical memory of the client data, an adversary can reverse-engineer those weights to reconstruct properties of the private training set.

#### Architecture & Methodology
* **Network Topology:** A 4-layer Multi-Layer Perceptron (MLP) mapping 784 input features (flattened $28 \times 28$ images) through hidden layers of 256, 128, and 64 units down to 10 class logits.
* **Attack Method:** Optimization-based Model Inversion (Activation Maximization). The trained weights of the network are completely frozen. A random noise tensor is fed as a dummy input, and gradient descent is used to optimize the input pixels to maximize the network's confidence scores for target classes.

#### Implementation Files
The implementation is split into two sequential Jupyter Notebooks:
1. **[Internship_Fed_1.ipynb](Internship_Fed_1.ipynb):** Handles the pipeline setup, local training on the MNIST dataset, and serialization of the resulting client weights (`fedavg_client_weights.pth`).
2. **[Internship_Fed_2.ipynb](Internship_Fed_2.ipynb):** Loads the frozen target architecture and saved weights, executes the multi-class optimization loop, and dynamically visualizes the ghostly reconstructed features of the targeted digits.


### Part 3: Robust Aggregation & Differential Privacy (DP) Defenses
Following the successful reconstruction attacks, we shifted focus to implementing and stress-testing industry-standard defense mechanisms. 

A core objective of this phase was to test the hypothesis that **data modality dictates privacy resilience**. We evaluated our defenses across two data types:
* **Integrated/Dense Data:** High-dimensional images (MNIST).
* **Scattered/Tabular Data:** Low-dimensional, heterogeneous features (Breast Cancer Dataset).

#### 1. Task 2: Trimmed Mean (Quantile) Aggregation
Standard Federated Averaging is highly vulnerable to data poisoning from malicious or skewed clients. To mitigate this, we implemented a Trimmed Mean aggregation strategy at the server level.
* **Mechanism:** The server sorts all client parameter updates and discards the extreme outliers (the top 5% and bottom 5% of updates). The remaining 90% is averaged to form a safe, robust global model.
* **Implementation File:**(Fed_Trimmed_Mean_Aggregation.ipynb)

#### 2. Task 3: Gradient Clipping & DP-SGD
As an alternative to quantile filtering, we implemented a strict mathematical constraint on client updates, forming a standard Differential Privacy pipeline.
* **Mechanism:** The central server calculates the magnitude ($L_2$ norm) of each client's proposed update. If the update exceeds a rigid threshold, it is mathematically scaled down. 
* **Implementation File:**(Fed_Gradient_Clipping.ipynb)

#### Key Findings: Data Modality vs. Differential Privacy
In both notebooks, after securing the aggregation step, the central server injected statistical noise to mask individual client contributions. We compared standard Gaussian noise against heavy-tailed Laplace noise. 

The experiments yielded conclusive evidence regarding data structures and privacy noise:
1. **Integrated Data is Fragile:** Dense image networks (MNIST) suffered catastrophic forgetting under heavy-tailed noise. In both experiments, injecting Laplace noise plummeted the image model's accuracy to unusable levels (dropping below 50%).
2. **Scattered Data is Robust:** The tabular networks (Breast Cancer features) showed incredible resilience. They comfortably absorbed aggressive Laplace noise, maintaining exceptional >96% accuracy across all experiments.

**Conclusion:** Robust aggregation protects against poisoning, but statistical privacy mechanisms must be uniquely tailored to the data's underlying structure.
