# Chapter 44 — Case Study: Active Learning for Experiment Selection

---

## Overview

This case study demonstrates active learning in practice — using a model to guide which experiments to run next, iteratively improving the model and converging on optimal compounds or conditions faster than random exploration.

## The Question

You are optimizing a series of compounds for potency against a target protein. You have a virtual library of 5,000 candidate compounds but can only synthesize and test 200. Which 200 should you choose?

Task type: active learning for regression (predicting pIC50).

## Setup

- **Seed set:** 50 compounds selected by maximum diversity (Tanimoto distance) from the 5,000-compound library, synthesized and tested. pIC50 values range from 4.5 to 7.2.
- **Representation:** Morgan fingerprints (radius 2, 2048 bits)
- **Model:** Random forest ensemble (10 models with different random seeds)
- **Acquisition function:** Upper confidence bound (predicted pIC50 + 1.5 × ensemble standard deviation)

## Active Learning Cycle

**Round 1 (50 seed compounds):**
- Train model on 50 compounds
- Score all 4,950 remaining candidates
- Select top 30 by acquisition function
- Synthesize and test → best pIC50 = 7.5

**Round 2 (80 compounds):**
- Retrain on 80 compounds
- Score remaining 4,920 candidates
- Select top 30 → best pIC50 = 8.1

**Round 3 (110 compounds):**
- Retrain on 110 compounds
- Select top 30 → best pIC50 = 8.4

**Round 4 (140 compounds):**
- Retrain on 140 compounds
- Select top 30 → best pIC50 = 8.6

**Round 5 (170 compounds):**
- Retrain on 170 compounds
- Select top 30 → best pIC50 = 8.7 (marginal improvement; convergence)

**Total: 200 compounds tested. Best pIC50 = 8.7.**

## Comparison to Random Selection

A parallel experiment using random selection of 200 compounds from the same library achieves a best pIC50 of 7.8. Active learning found a compound nearly 10-fold more potent using the same experimental budget.

## Balancing Exploration and Exploitation

- **Pure exploitation** (selecting by predicted pIC50 only) converges quickly to a local optimum (pIC50 = 8.0 by round 3) but misses better compounds in unexplored regions.
- **Pure exploration** (selecting by uncertainty only) explores broadly but wastes experiments on uninteresting compounds.
- **Upper confidence bound** balances both, achieving the best final result.

## Lessons

1. Active learning found a 10-fold more potent compound than random selection with the same experimental budget.
2. The seed set matters. A diverse seed set gives the model broad initial coverage.
3. The acquisition function matters. Balancing exploration and exploitation outperforms either alone.
4. Convergence is visible. When the improvement per round diminishes, the optimization is approaching its limit.
5. The model improves with each round. Early predictions are rough; later predictions are more accurate because the model has more data.

## Exercises

1. Vary the seed set size (20, 50, 100). How does it affect the final result?
2. Compare upper confidence bound to expected improvement as acquisition functions. Which performs better?
3. What happens if you use a GNN instead of a random forest as the base model? Does it improve active learning performance?
4. At what round would you stop the active learning cycle? How would you decide?
5. The active learning model consistently selects compounds from one chemical series. How would you encourage exploration of other series?
