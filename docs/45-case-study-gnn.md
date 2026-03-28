# Chapter 45 — Case Study: A First Graph Neural Network for Molecular Property Prediction

---

## Overview

This case study walks through building a graph neural network from scratch for molecular property prediction, comparing it to fingerprint baselines, and determining when the extra complexity is worth it.

## The Question

Predict the lipophilicity (logD at pH 7.4) of drug-like compounds from molecular structure.

Task type: regression.

## Data

- 4,200 compounds with measured logD values from a pharmaceutical dataset
- Split: scaffold split, 80/20

## Graph Construction

Each molecule is converted to a graph using RDKit:
- **Node features (per atom):** element type (one-hot, 10 elements), formal charge, number of hydrogens, hybridization (one-hot), aromaticity, ring membership — total 20 features per atom
- **Edge features (per bond):** bond type (one-hot: single, double, triple, aromatic), ring membership, conjugation — total 6 features per bond

## Baselines

| Model | RMSE | R² |
|---|---|---|
| Dummy (mean) | 1.45 | 0.00 |
| 1-NN (Morgan FP) | 0.92 | 0.60 |
| Ridge regression (descriptors) | 0.78 | 0.71 |
| Random forest (Morgan FP) | 0.68 | 0.78 |
| Gradient boosting (descriptors + FP) | 0.64 | 0.81 |

## GNN Architecture

- 3 message-passing layers (MPNN-style)
- Hidden dimension: 128
- Sum pooling for graph-level readout
- 2-layer dense prediction head
- Dropout: 0.2
- Training: Adam optimizer, learning rate 1e-3, early stopping on validation loss

## GNN Results

| Model | RMSE | R² |
|---|---|---|
| GNN (graph only) | 0.62 | 0.82 |
| GNN + global descriptors | 0.59 | 0.84 |

The GNN provides a modest improvement over gradient boosting (0.62 vs. 0.64 RMSE). Adding global descriptors (molecular weight, cLogP, TPSA) as additional features to the readout vector provides a further small improvement.

## When Is the GNN Worth It?

**Analysis by test set diversity:**
- For test compounds with Tanimoto > 0.5 to nearest training compound: GNN and gradient boosting perform similarly (RMSE ~0.55 vs. 0.57)
- For test compounds with Tanimoto < 0.3 to nearest training compound: GNN outperforms gradient boosting more clearly (RMSE 0.78 vs. 0.88)

The GNN's advantage is most pronounced for structurally novel compounds — those far from the training set. For close analogs, fingerprint-based models are nearly as good.

## Lessons

1. The GNN provides a real but modest improvement over well-tuned fingerprint models for this task.
2. The GNN's advantage is largest for structurally diverse test compounds, where fingerprints are less informative.
3. Adding global descriptors to the GNN improves performance, suggesting that the GNN's learned features and hand-crafted descriptors capture complementary information.
4. The GNN requires more computation (10x training time) and more expertise to implement and tune.
5. For this dataset size (4,200 compounds), the GNN is viable but not dramatically better than classical approaches. With larger datasets, the advantage would likely grow.

## When the Extra Complexity Is Not Worth It

If your dataset is small (< 1,000 compounds), your test set is structurally similar to the training set, or you need maximum interpretability, a random forest on fingerprints is the better choice. The GNN's advantage is real but situational.

## Exercises

1. Vary the number of message-passing layers (1, 2, 3, 4, 5). How does performance change? At what depth does oversmoothing appear?
2. Compare sum pooling, mean pooling, and attention-based pooling. Which works best?
3. Remove bond features from the graph. How much does performance drop?
4. Train the GNN on datasets of different sizes (500, 1000, 2000, 4200). At what size does the GNN start to outperform the random forest?
5. Visualize the learned atom embeddings using UMAP. Do chemically similar atoms cluster together?
