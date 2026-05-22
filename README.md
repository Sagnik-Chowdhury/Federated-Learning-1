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

### Sagnik's Experiment: 

### 1. Federated Learning with Standard and Delta Weight Aggregation

This experiment explores Federated Learning on the CIFAR-100 dataset using two different communication strategies: traditional weight sharing and Delta Weight sharing. The objective is to analyze how model aggregation methods affect collaborative learning performance under Non-IID client distributions.

#### Architecture & Methodology
##### Network Topology

  * The experiment uses a ResNet-18 convolutional neural network for image classification on the CIFAR-100 dataset containing 100 object classes.

  * The model is trained in a decentralized Federated Learning environment where multiple simulated clients perform local training independently.

##### Federated Learning Setup:
  * **Dataset**: CIFAR-100
  * **Client Distribution**: Non-IID
  * **Global Aggregation**: Federated Averaging (FedAvg)
  * **Communication Type**:
      * Standard Full Weight Sharing
      * Delta Weight Sharing
   
### Delta Weight Strategy

In the second experiment, clients transmit only the weight updates instead of the full model parameters.

The delta update is computed as:  delta_w = local_weights - global_weights


This reduces redundant communication and focuses only on the learned parameter changes.

### Implementation Files

The implementation is divided into two Jupyter Notebooks:

1. - [Standard Federated Learning](FL_CIFAR_100_ResNet_18.ipynb): Implements standard Federated Learning using full model weight aggregation with FedAvg on the CIFAR-100 dataset.
2. - [Delta Weight Federated Learning](FL_CIFAR_100_ResNet_18_Delta.ipynb): Implements Federated Learning using Delta Weight aggregation, where clients communicate only parameter updates instead of complete model weights.
  
## 2.  Dirichlet-Based Non-IID Federated Learning

[Dirichlet](FL_CIFAR_100_ResNet_18_Dirichlet.ipynb) :   Implemented a Federated Learning framework using ResNet-18 on the CIFAR-100 dataset with Dirichlet-based Non-IID client data partitioning. The project simulates realistic heterogeneous client distributions and trains a global model using the FedAvg aggregation algorithm across multiple communication rounds.
  
## 3. FedProx-Based Federated Learning

This project implements the FedProx algorithm for federated learning using a ResNet-18 model on the CIFAR-100 dataset under a non-IID Dirichlet data distribution. The experiment simulates decentralized client training where each client trains locally on heterogeneous data while a central server aggregates model updates over multiple communication rounds.

Unlike traditional FedAvg, FedProx introduces a proximal regularization term to reduce client drift and improve optimization stability in heterogeneous federated environments. The project evaluates global model convergence, client-side losses, and overall classification performance across distributed clients.

### Key Features
- Non-IID Dirichlet data partitioning
- Federated client-server training architecture
- FedProx optimization with proximal regularization
- ResNet-18 based image classification
- CIFAR-100 dataset support
- Global accuracy and loss tracking

### Results
The model achieved a best global accuracy of **22.47%** after 25 communication rounds under heterogeneous client distributions.

[FedProx](FL_CIFAR_100_ResNet_18_FedProx.ipynb) : Implements the FedProx algorithm for Federated Learning on the CIFAR-100 dataset under non-IID Dirichlet data distribution. FedProx introduces a proximal regularization term to reduce client drift and improve training stability in heterogeneous federated environments.
