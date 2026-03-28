# Chapter 43 — Case Study: Reaction Yield Modeling

---

## Overview

This case study tackles reaction yield prediction — a multimodal problem where molecular structure and reaction conditions both matter. It illustrates the challenges of condition-dependent chemistry, split strategy for reactions, and the limits of yield prediction.

## The Question

Predict the yield of Buchwald-Hartwig amination reactions from reactant structures and reaction conditions, to guide condition optimization for new substrates.

Task type: regression.

## Data

- 4,000 reactions from a high-throughput experimentation (HTE) dataset
- Features: aryl halide (SMILES), amine (SMILES), catalyst, ligand, base, solvent, temperature
- Target: yield (0–100%)
- Noise: replicate standard deviation ~5% yield

## Representation

- Reactant structures: Morgan fingerprints (radius 2, 1024 bits) for each reactant, concatenated
- Conditions: one-hot encoding for categorical variables (catalyst, ligand, base, solvent) + continuous temperature

## Split Strategy

Three split strategies compared:
1. **Random split:** reactions randomly assigned to train/test
2. **Substrate split:** all reactions with a given aryl halide in the same split
3. **Condition split:** all reactions with a given catalyst/ligand combination in the same split

## Results

| Model | Random RMSE | Substrate RMSE | Condition RMSE |
|---|---|---|---|
| Dummy (mean yield) | 28.5 | 28.5 | 28.5 |
| Linear regression | 15.2 | 19.8 | 18.5 |
| Random forest | 11.3 | 17.2 | 15.8 |
| Gradient boosting | 10.5 | 16.5 | 15.0 |

The gap between random and substrate splits is large (10.5 vs. 16.5), indicating that the model relies heavily on substrate-specific patterns. The condition split is intermediate.

## Multimodal Approach

Replacing fingerprint concatenation with a GNN for reactant encoding + dense network for conditions:
- Random RMSE: 9.8
- Substrate RMSE: 15.5

The GNN provides a modest improvement, primarily for structurally diverse substrates.

## Lessons

1. Reaction yield prediction is inherently noisy. The experimental noise floor (~5%) limits achievable accuracy.
2. Split strategy matters enormously. Random splits overestimate performance for new substrates.
3. Conditions are as important as structures. A structure-only model performs much worse than a structure + conditions model.
4. The model is most useful for ranking conditions (which conditions are likely best for a new substrate?) rather than predicting exact yields.
5. Publication bias in reaction databases means that failed reactions are underrepresented, potentially biasing models toward optimistic yield predictions.

## Exercises

1. Compare a structure-only model to a structure + conditions model. How much do conditions contribute?
2. Which conditions (catalyst, ligand, base, solvent, temperature) are most important for yield prediction?
3. The model predicts 85% yield for a new reaction. The experimental result is 45%. What might explain this discrepancy?
4. Design a split strategy that tests the model's ability to generalize to both new substrates and new conditions simultaneously.
5. How would you combine this yield prediction model with an active learning loop to optimize conditions for a new substrate?
