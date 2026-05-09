# Literature Summary: Communication-Efficient Learning of Deep Networks

**Collaborators:** Sourit Mitra & Sagnik Chowdhury

## 1. Overview & The Core Problem
[cite_start]Modern mobile devices generate a massive wealth of data (e.g., text, images) that is ideal for training intelligent models, but this data is often highly privacy-sensitive and bandwidth-heavy[cite: 6, 8]. [cite_start]Traditional machine learning requires logging this raw data to a centralized data center, which introduces significant privacy risks and communication bottlenecks[cite: 8, 49]. 

[cite_start]This paper introduces the problem of training on decentralized data and proposes a practical algorithm to solve it[cite: 30, 31]. [cite_start]The primary advantage of this approach is the decoupling of model training from the need for direct access to the raw training data[cite: 27].

## 2. The Federated Learning Architecture
[cite_start]To address these constraints, the authors propose **Federated Learning**, a decentralized approach where the learning task is solved by a loose federation of participating devices (clients) coordinated by a central server[cite: 22]. 

The architecture operates under the principle of data minimization:
* [cite_start]Each client maintains a local training dataset that is **never** uploaded to the server[cite: 23].
* [cite_start]The central server sends the current global model to a random fraction of clients[cite: 73].
* [cite_start]Clients compute an update to the model locally and transmit only this ephemeral update back to the server over a mix network[cite: 24, 52, 56].
* [cite_start]Once the server aggregates the updates to improve the global model, the individual updates are discarded[cite: 26].

## 3. Federated Optimization & The Mathematical Objective
[cite_start]Federated optimization differs drastically from traditional distributed optimization (like data center clusters) due to four key properties[cite: 58, 59]:
1. [cite_start]**Non-IID:** A user's local dataset represents their personal usage and is not a representative sample of the overall population distribution[cite: 60].
2. [cite_start]**Unbalanced:** Users interact with apps at different rates, leading to vastly different amounts of local training data per client[cite: 61].
3. [cite_start]**Massively Distributed:** The number of participating clients is exponentially larger than the average number of examples per client[cite: 62].
4. [cite_start]**Limited Communication:** Mobile devices operate on slow, expensive, or intermittent connections[cite: 63, 64].

To frame this mathematically, the algorithm assumes any standard finite-sum objective (like minimizing the loss of a prediction). If $n$ is the total number of data points across all devices, the global objective is:
$$f(w) = \frac{1}{n} \sum_{i=1}^n f_i(w)$$

In a federated setting with $K$ clients, the data is partitioned. If client $k$ holds $n_k$ data points, the local objective for that specific client is $F_k(w)$:
$$F_k(w) = \frac{1}{n_k} \sum_{i \in \mathcal{P}_k} f_i(w)$$

This allows us to rewrite the global objective as a weighted average of the local client objectives:
$$f(w) = \sum_{k=1}^K \frac{n_k}{n} F_k(w)$$

[cite_start]If the data were uniformly distributed (IID), the expected value of any local $F_k(w)$ would equal the global $f(w)$[cite: 91]. [cite_start]However, in this non-IID environment, $F_k(w)$ could be a very poor approximation of $f(w)$, making the optimization highly complex[cite: 93].

## 4. Overcoming Communication Bottlenecks
[cite_start]Because mobile computation (CPUs/GPUs) is essentially free compared to the heavy cost of mobile network communication, the primary goal is to use additional on-device computation to dramatically decrease the number of communication rounds required[cite: 98, 99]. 

This is achieved by:
* [cite_start]**Increased Parallelism:** Using more clients independently between rounds[cite: 100].
* [cite_start]**Increased Local Computation:** Having each client perform a complex calculation (multiple epochs of training) rather than a simple single gradient calculation before sending the update[cite: 101].
