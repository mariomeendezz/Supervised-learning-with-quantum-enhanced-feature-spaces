# Variational Quantum Classifier

This project implements and evaluates a variational quantum classifier inspired by Havlíček et al., [*Supervised Learning with Quantum-Enhanced Feature Spaces*](https://arxiv.org/abs/1804.11326). The implementation studies how variational-circuit depth affects binary classification performance in simulation and prepares a later fine-tuning stage on real quantum hardware.

## Methodology

### Dataset

The dataset is generated artificially from two-dimensional points in the domain $(0, 2\pi]^2$. Each point is encoded into a two-qubit quantum state using the feature map proposed in the paper:

$$
\mathcal{U}_{\Phi}(\mathbf{x})
= U_{\Phi}(\mathbf{x})H^{\otimes n}
  U_{\Phi}(\mathbf{x})H^{\otimes n}.
$$

Binary labels $+1$ and $-1$ are assigned from the expectation value of a parity observable transformed by a random unitary in $SU(4)$. Points close to the decision boundary are discarded using a gap of $\Delta=0.3$, producing a balanced and clearly separated dataset. The samples are then divided into stratified training and test sets.

### Variational classifier

The classifier applies the quantum feature map followed by a parameterized ansatz. Each variational layer contains:

- General single-qubit rotations.
- CZ gates that entangle connected qubits.

The parity of the measured bitstrings determines the predicted class. The circuit parameters and a trainable classification bias are optimized with SPSA using an approximation of the empirical classification risk.

### Execution modes

The notebook supports two execution modes through the `USE_ESTIMATOR` flag:

- **Estimator-based (`USE_ESTIMATOR = True`):** uses `StatevectorEstimator` to compute expectation values directly, without finite-shot sampling noise.
- **Shot-based (`USE_ESTIMATOR = False`):** runs measured circuits with a sampler and estimates class probabilities from a finite number of measurement shots.

### Experiments

The simulated experiments compare ansätze with one to four variational layers. For each depth, the notebook records the optimization loss and evaluates classification accuracy on the test set.

The best parameters obtained in simulation for the one-layer model will subsequently be used to initialize execution on real IBM Quantum hardware. Fine-tuning will then be performed directly on the device to investigate whether adapting the parameters to its noise and hardware characteristics improves the results.


## Installation

Create and activate a virtual environment:

```bash
python -m venv .venv
```

On Windows:

```powershell
.venv\Scripts\Activate.ps1
```

On macOS or Linux:

```bash
source .venv/bin/activate
```

Install the dependencies:

```bash
python -m pip install -r requirements.txt
```

## Usage

Open `main.ipynb` in Jupyter Notebook or JupyterLab and execute its cells sequentially. Later cells reuse the datasets, circuits, optimized parameters, and other objects created earlier in the notebook.

The current workflow runs with Qiskit Aer or an exact statevector estimator. Access to an IBM Quantum account will be required for the planned hardware fine-tuning stage.

## Reference

V. Havlíček et al., “Supervised learning with quantum-enhanced feature spaces” *Nature*, vol. 567, pp. 209–212, 2019. [arXiv:1804.11326](https://arxiv.org/abs/1804.11326).

## License

This project is available under the terms of the [MIT License](LICENCE.txt).