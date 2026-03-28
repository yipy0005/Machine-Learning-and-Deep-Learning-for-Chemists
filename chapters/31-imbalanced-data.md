# Chapter 31 — Imbalanced Data and Rare Events

---

## Chemical Motivation

In a typical high-throughput screen, fewer than 1% of compounds are active. In toxicology, most compounds are non-toxic. In materials screening, most candidates fail to meet the target specification. The interesting events — the actives, the toxins, the hits — are rare. This class imbalance creates serious challenges for ML models that, left unaddressed, will produce models that are useless for the very task they were built for.

## Core Concept

### The Problem with Imbalance

When one class vastly outnumbers the other, a model can achieve high accuracy by simply predicting the majority class for everything. A model that always predicts "inactive" on a dataset with 1% actives achieves 99% accuracy — and finds zero hits.

This is why accuracy is a misleading metric for imbalanced data (Chapter 17). The model needs to be evaluated on its ability to find the rare class, not on its overall correctness.

### Threshold Selection

Most classifiers output a continuous score (probability or confidence) rather than a binary prediction. The threshold at which you convert this score to a binary decision (active/inactive) is a critical choice. Lowering the threshold increases recall (finding more actives) but decreases precision (more false positives). Raising the threshold does the opposite.

The optimal threshold depends on the relative costs of false positives and false negatives. In drug screening, a false negative (missing a potential drug) may be far more costly than a false positive (testing an inactive compound). This argues for a lower threshold.

### Strategies for Handling Imbalance

**Class weighting** — assign higher weight to the minority class during training, so the model pays more attention to rare events. This is like increasing the concentration of a dilute analyte to improve detection.

**Resampling** — oversample the minority class (duplicate rare examples) or undersample the majority class (remove common examples) to balance the training set. Oversampling risks overfitting to the minority class; undersampling discards potentially useful data.

**Threshold optimization** — train the model normally but optimize the decision threshold on validation data to maximize the metric that matters (e.g., F1, enrichment).

**Specialized metrics** — use PR-AUC, F1, or enrichment factor instead of accuracy or ROC-AUC.

## Chemist-Friendly Intuition

Imbalanced data is like searching for a needle in a haystack. The model needs to find the needles (actives) without being overwhelmed by the hay (inactives). A model that reports "no needles found" is technically accurate (the haystack is mostly hay) but completely useless.

Class weighting is like using a metal detector — it amplifies the signal from the needles relative to the hay. Resampling is like reducing the haystack or adding more needles to make the search easier. Threshold optimization is like adjusting the detector's sensitivity.

## Concrete Chemical Example

You have a screening dataset with 100,000 compounds, of which 500 (0.5%) are active. You train a random forest classifier:

- With default settings: accuracy = 99.5%, but recall = 10% (finds only 50 of 500 actives). Useless for screening.
- With class weighting (10:1 for actives): accuracy = 95%, recall = 60% (finds 300 of 500 actives). Much more useful.
- With threshold optimization (threshold = 0.3 instead of 0.5): recall = 75%, precision = 5%. Finds 375 actives but also flags 7,125 inactives. Whether this is acceptable depends on the cost of follow-up testing.

## What Can Go Wrong

- **Using accuracy as the primary metric.** Always use precision, recall, F1, PR-AUC, or enrichment for imbalanced data.
- **Oversampling without care.** Naive oversampling (duplicating minority examples) can cause overfitting. Consider SMOTE or other synthetic oversampling methods.
- **Ignoring the cost structure.** The optimal threshold depends on the relative costs of false positives and false negatives. These costs are domain-specific and should be estimated before modeling.
- **Evaluating on balanced subsets.** Artificially balancing the test set gives misleading performance estimates. Evaluate on the natural class distribution.

## Practical Decision Rules

1. **Never use accuracy for imbalanced data.** Use PR-AUC, F1, or enrichment.
2. **Apply class weighting** as a default strategy for imbalanced classification.
3. **Optimize the decision threshold** on validation data using the metric that matches your decision.
4. **Report the class distribution** alongside all metrics.
5. **Consider the cost of errors** when choosing between precision and recall.

## Mini-Summary

In many chemistry settings, the interesting events are rare — active compounds, toxic compounds, successful reactions. Standard ML approaches and metrics fail on imbalanced data because they are dominated by the majority class. Class weighting, resampling, threshold optimization, and appropriate metrics (PR-AUC, enrichment) are essential tools for handling imbalance. Always evaluate models on their ability to find the rare class, not on overall accuracy.

## Exercises and Thought Questions

1. A dataset has 0.1% actives. What accuracy does a model that always predicts "inactive" achieve? Why is this misleading?
2. Compare class weighting and oversampling for a dataset with 2% actives. Which strategy works better? Does it depend on the model type?
3. You can test 500 compounds from a library of 50,000. What enrichment factor would make the model useful? How does this relate to the hit rate?
4. Plot precision vs. recall at different thresholds for your model. Where is the optimal operating point for your specific use case?
5. A colleague reports F1 = 0.80 on an imbalanced dataset. Is this good? What additional information do you need to judge?
