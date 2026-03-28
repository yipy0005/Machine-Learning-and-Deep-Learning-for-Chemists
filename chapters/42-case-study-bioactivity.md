# Chapter 42 — Case Study: Predicting Bioactivity in a Screening Campaign

---

## Overview

This case study addresses a classification problem: predicting whether compounds are active or inactive against a biological target, using data from a high-throughput screening campaign. It illustrates the challenges of imbalanced data, scaffold splits, and the interpretation of false positives and false negatives.

## The Question

Given a library of 50,000 compounds screened against a kinase target, build a model to predict activity (IC50 < 1 μM = active) for untested compounds, to prioritize a virtual library of 500,000 compounds for experimental testing.

Task type: binary classification with severe class imbalance.

## Data

- 50,000 compounds screened; 750 active (1.5%), 49,250 inactive
- Representation: Morgan fingerprints (radius 2, 2048 bits)
- Split: scaffold split, 80/20

## Baselines and Models

| Model | PR-AUC | Recall@100 | Enrichment@1% |
|---|---|---|---|
| Random (baseline) | 0.015 | 1.5 | 1.0 |
| 1-NN (Tanimoto) | 0.18 | 12 | 8.0 |
| Logistic regression | 0.22 | 15 | 10.0 |
| Random forest | 0.31 | 22 | 14.7 |
| Gradient boosting | 0.34 | 25 | 16.7 |

The gradient boosting model finds ~25 actives in the top 100 predictions — a 16.7x enrichment over random selection.

## Handling Imbalance

- Class weighting (10:1 for actives) improves recall from 40% to 65% at the cost of precision (from 15% to 8%).
- Threshold optimization: lowering the decision threshold from 0.5 to 0.15 increases recall to 70% while maintaining enrichment in the top-ranked compounds.

## Interpretation of Errors

**False positives** (predicted active, actually inactive): Often structurally similar to true actives but lacking a critical pharmacophoric feature. These are the "near misses" — compounds that look right but are not.

**False negatives** (predicted inactive, actually active): Often from scaffolds not well-represented in the training set. These are the compounds the model cannot predict because it has not seen similar chemistry.

## Lessons

1. PR-AUC and enrichment are far more informative than accuracy for this problem.
2. Scaffold splits reveal that the model struggles with novel scaffolds — a realistic assessment of deployment performance.
3. Class weighting and threshold optimization are essential for imbalanced screening data.
4. False negatives cluster in underrepresented scaffolds, suggesting that expanding the training set's chemical diversity would improve recall.
5. The model is most useful for prioritizing compounds within known chemical space, not for discovering entirely new chemotypes.

## Exercises

1. Compare random split and scaffold split performance. How large is the gap?
2. Plot the precision-recall curve and identify the optimal operating point for your screening budget.
3. Examine the false negatives. What scaffolds are they from? Are these scaffolds represented in the training set?
4. Add molecular descriptors to the fingerprint features. Does this improve performance?
5. How would you use this model in practice? What would you test first?
