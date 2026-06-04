# Federated Learning: Privacy Attacks & Modality‑Aware Robust Aggregation



This branch contains my individual research on **security vulnerabilities and robust aggregation strategies** in Federated Learning. The work is divided into two major thrusts:

1. **Model Inversion Attacks** – demonstrating that shared model weights leak private client data.
2. **Defensive Aggregation** – implementing and stress‑testing five algorithms against noise, data skew, and poisoning, with a focus on how **data modality** (dense images vs scattered tabular features) affects resilience.

All experiments are fully reproducible. Each notebook below contains the complete pipeline: data loading, client partitioning, federated rounds, aggregation, and evaluation.

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
  - *Image Model:* A 4‑layer MLP mapping 784 input pixels down to 10 class logits.
  - *Tabular Model:* A 3‑layer MLP mapping 30 standardized medical features to 2 diagnosis classes.

- **Attack Method:** Optimization‑based Model Inversion (Activation Maximization). The trained weights of the network are completely frozen on the server side. Pure random noise is fed as a dummy input, and gradient descent is used to optimize the input values to maximize the network’s confidence scores for specific target classes.

#### Implementation Files

The implementation is split into two sequential Jupyter Notebooks:

- **[`Model Construction`](Model_Construction.ipynb)** – Handles the pipeline setup, constructs the neural networks for both datasets, performs local training, and serializes the resulting client weights (`fedavg_mnist_weights.pth` and `fedavg_tabular_weights.pth`).
- **[`Model Inversion`](Model_Inversion.ipynb)** – Loads the frozen target architectures and saved weights, then executes the inversion loop. It dynamically visualizes the ghostly reconstructed pixels for the MNIST dataset, and generates archetypal feature‑importance bar charts for the tabular dataset.

---

## Robust Aggregation & Defenses – Notebook Descriptions

After confirming the inversion vulnerability, I implemented and evaluated five aggregation strategies under three stress conditions: statistical noise, non‑IID data skew, and label‑flipping attacks. Below is a concise description of what each notebook does.

### 1. [`Trimmed Mean`](Trimmed_Mean.ipynb)
This notebook implements the **Trimmed Mean** aggregation rule, which discards the top and bottom 10% of client parameter updates per coordinate before averaging. The goal is to filter out extreme outliers – whether from data poisoning or natural heterogeneity. I tested this defense on both MNIST and Breast Cancer datasets, injecting Gaussian and Laplace noise at the server after aggregation, and measured how well the trimmed mean protected model utility.

### 2. `Fed_Gradient_Clipping.ipynb`
Here I implemented **Gradient Clipping** with a novel twist: the clipping threshold is not tuned manually but derived from the **80th percentile** of client weight L2 norms in the **first federated round**. This fixed threshold is then reused for all subsequent rounds. The notebook clips each client’s entire weight vector to this bound before averaging. I then added Gaussian and Laplace noise to evaluate the combined effect on dense vs tabular data.

### 3. `Fedprox.ipynb`
This notebook implements **FedProx**, which adds a proximal penalty (`μ = 0.01`) to each client’s local loss. The penalty anchors the local model to the current global model, reducing “client drift” caused by heterogeneous data. I trained clients on both data modalities, with and without server‑side noise injection, to see whether the proximal term helps stabilise learning under statistical noise.

### 4. `Scaffolding.ipynb`
Here I implemented **SCAFFOLD** – a more advanced drift‑correction method that uses control variates at both the server and client sides. During local training, clients modify their gradients using the difference between the global control variate and their local control variate. This notebook runs SCAFFOLD on MNIST and Breast Cancer under the same noise conditions, revealing its sensitivity to both data modality and injected noise.

### 5. `IID_Experiment_CIFAR10.ipynb`
This notebook establishes a baseline for all five strategies (FedAvg, Trimmed Mean, Gradient Clipping, FedProx, SCAFFOLD) on CIFAR‑10 under **ideal IID conditions** – each client receives exactly the same number of samples with identical class distribution. I ran 25 communication rounds and recorded final test accuracy to see which strategy performs best when no statistical heterogeneity or poisoning is present.

### 6. `NonIID_Experiment_CIFAR10.ipynb`
To test resilience to **statistical heterogeneity**, I partitioned CIFAR‑10 using a Dirichlet distribution with α values of 0.05, 0.15, 0.25, 0.35, and 0.45. Lower α means more extreme skew (some clients may see only one or two classes). I ran all five strategies for 25 rounds and compared final accuracies across α levels. This notebook revealed which algorithms survive severe non‑IID conditions.

### 7. `Data_Poisoning_Label_Flipping.ipynb`
Finally, I simulated a **Byzantine attack** – label flipping – where two out of ten clients (20% compromise) systematically flip labels during training (`new_label = (original_label + 5) % 10`). The server receives updates from both honest and malicious clients. This notebook runs all five strategies for 10 rounds and measures how much each aggregation rule can mitigate the poisoning effect.

---

## Key Conclusions from the Experiments

- **Noise vulnerability:** Dense image data (MNIST) collapses under heavy‑tailed Laplace noise regardless of the defense. Tabular data (Breast Cancer) remains highly accurate even with strong noise.
- **Gradient Clipping with fixed 80th‑percentile threshold:** Performs exceptionally well under extreme non‑IID (α=0.05), outperforming FedAvg. It is simple, requires no per‑round tuning, and is stable.
- **SCAFFOLD is fragile:** It diverges under severe skew (α ≤ 0.15) and performs poorly even on tabular data. Not recommended for real‑world heterogeneous environments.
- **Label flipping:** All methods lose about 15% accuracy compared to the clean IID baseline. Trimmed Mean and FedProx offer only marginal gains (~1%). Stronger Byzantine defenses would be needed for higher compromise rates.

---

## How to Run

1. Clone the repository and switch to the `Sourit` branch:
   ```bash
   git clone https://github.com/Sagnik-Chowdhury/Federated-Learning-1.git
   cd Federated-Learning-1
   git checkout Sourit
