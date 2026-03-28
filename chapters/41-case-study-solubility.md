# Chapter 41 — Case Study: Building a Solubility Prediction Model

## Part IX — Guided Case Studies

---

## Overview

This case study walks through a complete ML project for predicting aqueous solubility — one of the most fundamental and well-studied problems in cheminformatics. It illustrates every step of the workflow from Chapter 40 with concrete decisions and realistic challenges.

## The Question

Predict the aqueous solubility (log S, mol/L) of drug-like compounds from molecular structure, to support early-stage compound prioritization in a drug discovery program.

Task type: regression.

## Data Assembly and Audit

**Source:** A curated dataset of 3,000 compounds with experimentally measured aqueous solubility from multiple literature sources.

**Audit findings:**
- 150 duplicate SMILES entries (removed, keeping the median value)
- 200 compounds with multiple measurements from different sources; median disagreement = 0.6 log units (this is the noise floor)
- 50 compounds with solubility values that disagree by > 2 log units across sources (flagged for review; 30 removed as unreliable)
- Final dataset: 2,820 compounds

**Noise floor:** ~0.5–0.6 log units. Any model with RMSE near this value is approaching the limit of the data.

## Representation Choice

We test three representations:
1. **200 RDKit descriptors** — physicochemical properties, topological indices, constitutional descriptors
2. **Morgan fingerprints** (radius 2, 2048 bits) — substructural features
3. **Descriptors + fingerprints concatenated** — combined information

## Evaluation Strategy

- **Split:** Scaffold split (Murcko scaffolds). 80% train, 20% test.
- **Cross-validation:** 5-fold cross-validation on the training set for model selection and hyperparameter tuning.
- **Metrics:** RMSE, MAE, R²

## Baselines

| Model | RMSE | MAE | R² |
|---|---|---|---|
| Dummy (predict mean) | 2.05 | 1.68 | 0.00 |
| 1-Nearest neighbor (Morgan FP) | 1.25 | 0.95 | 0.63 |
| Ridge regression (descriptors) | 0.98 | 0.74 | 0.77 |
| Random forest (descriptors) | 0.85 | 0.63 | 0.83 |

## Model Improvement

| Model | RMSE | MAE | R² |
|---|---|---|---|
| Random forest (FP) | 0.88 | 0.66 | 0.81 |
| Random forest (descriptors + FP) | 0.82 | 0.61 | 0.84 |
| Gradient boosting (descriptors + FP) | 0.79 | 0.58 | 0.85 |
| Dense neural network (descriptors + FP) | 0.80 | 0.59 | 0.85 |

The gradient boosting model provides the best performance, but the improvement over the random forest is modest.

## Uncertainty

An ensemble of 10 gradient boosting models (trained with different random seeds) provides uncertainty estimates. The ensemble standard deviation correlates with prediction error: compounds with high ensemble disagreement tend to have larger errors.

**Applicability domain:** Compounds with nearest-neighbor Tanimoto < 0.3 to the training set have significantly higher errors. These are flagged as out-of-domain.

## Interpretation

Feature importance analysis reveals:
- cLogP is the most important feature (expected: lipophilicity drives solubility)
- TPSA is the second most important (expected: polarity aids solubility)
- Molecular weight, number of aromatic rings, and number of rotatable bonds are also important

These align with chemical intuition, increasing confidence in the model.

## Decision

The gradient boosting model on combined descriptors and fingerprints achieves RMSE = 0.79 log units on a scaffold split — close to the noise floor of 0.5–0.6 log units. The model is suitable for compound prioritization (ranking compounds by predicted solubility) but not for precise quantitative prediction (the error is too large for formulation design).

## Lessons

1. Data curation removed ~6% of the dataset but improved data quality significantly.
2. The noise floor (0.5–0.6 log units) sets a realistic expectation for model performance.
3. Scaffold splits give lower performance than random splits (R² = 0.85 vs. ~0.92), but the scaffold split result is more honest.
4. The improvement from random forest to gradient boosting is small. For this problem, a random forest is nearly as good and simpler.
5. Combining descriptors and fingerprints provides a modest improvement over either alone.

## Exercises

1. Reproduce this case study with a public solubility dataset (e.g., ESOL or AqSolDB). Do your results match?
2. Add a GNN model to the comparison. Does it outperform the gradient boosting model?
3. Vary the scaffold split to use different scaffold definitions. How sensitive are the results?
4. Examine the compounds with the largest prediction errors. What characterizes them?
5. Deploy the model on a set of compounds from a different chemical space (e.g., natural products). How does performance change?
