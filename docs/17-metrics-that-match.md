# Chapter 17 — Metrics That Match the Decision

---

## Chemical Motivation

A model's performance is only meaningful in the context of the decision it supports. A solubility model used for rough triage needs different metrics than one used for quantitative formulation design. A virtual screening model that ranks compounds for testing needs different metrics than a classifier that flags toxic compounds for removal.

Choosing the wrong metric is like measuring the wrong endpoint in an assay — you get a number, but it does not answer your question.

## Core Concept

### Regression Metrics

**MAE (Mean Absolute Error)** — the average magnitude of prediction errors. Easy to interpret: "On average, predictions are off by X units." Robust to outliers.

**RMSE (Root Mean Squared Error)** — similar to MAE but penalizes large errors more heavily. Useful when large errors are particularly costly.

**R² (Coefficient of Determination)** — the fraction of variance in the target explained by the model. R² = 1 means perfect prediction; R² = 0 means the model is no better than predicting the mean. Useful for comparing models but can be misleading on small or non-representative test sets.

### Classification Metrics

**Accuracy** — the fraction of correct predictions. Misleading when classes are imbalanced (Chapter 31). If 95% of compounds are inactive, a model that always predicts "inactive" achieves 95% accuracy.

**Precision** — of all compounds predicted active, what fraction is truly active? High precision means few false positives. Important when follow-up experiments are expensive.

**Recall (Sensitivity)** — of all truly active compounds, what fraction did the model find? High recall means few false negatives. Important when missing an active compound is costly.

**F1 Score** — the harmonic mean of precision and recall. A balanced measure when both false positives and false negatives matter.

**ROC-AUC** — the area under the receiver operating characteristic curve. Measures the model's ability to rank actives above inactives across all possible thresholds. Useful for comparing models but can be optimistic when classes are highly imbalanced.

**PR-AUC (Precision-Recall AUC)** — the area under the precision-recall curve. More informative than ROC-AUC when the positive class is rare, which is common in screening.

### Ranking Metrics

**Top-k enrichment** — of the top k predictions, what fraction is truly active? Directly relevant for virtual screening, where you test only the top-ranked compounds.

**Enrichment factor** — how many times more actives are found in the top-ranked fraction compared to random selection. An enrichment factor of 10 means the model finds 10x more actives than random.

### Calibration

A calibrated model produces probabilities that match observed frequencies. If the model predicts 70% probability of activity for a group of compounds, approximately 70% of them should actually be active. Calibration is important when the predicted probability is used for decision-making, not just ranking.

## Chemist-Friendly Intuition

Think of metrics as different ways of grading an exam:

- **Accuracy** is the overall percentage correct — simple but misleading if most questions are easy.
- **Precision** is "of the answers you marked as correct, how many actually were?" — important when wrong answers have consequences.
- **Recall** is "of all the correct answers, how many did you find?" — important when missing correct answers is costly.
- **ROC-AUC** is "how well did you rank the correct answers above the incorrect ones?" — important when you are prioritizing.

In a drug screening context:
- High precision means fewer false positives — fewer wasted experiments on inactive compounds.
- High recall means fewer false negatives — fewer missed actives that could have been valuable leads.
- The right balance depends on the cost of experiments vs. the cost of missing a lead.

## Concrete Chemical Example

You are building a virtual screening model to identify kinase inhibitors from a library of 100,000 compounds. You plan to test the top 1,000 predictions experimentally.

The relevant metric is **top-1000 enrichment**: of the 1,000 compounds you test, how many are truly active? If the hit rate in the full library is 0.5% (500 actives out of 100,000), random selection would give you about 5 actives in 1,000 compounds. If your model gives you 50 actives in the top 1,000, the enrichment factor is 10.

In this scenario, ROC-AUC is less relevant because you do not care about the model's performance across the entire library — you only care about the top-ranked compounds. A model with lower ROC-AUC but better top-k enrichment may be more useful for your specific decision.

## What Can Go Wrong

- **Using accuracy for imbalanced data.** A model that always predicts the majority class achieves high accuracy but is useless. Use precision, recall, F1, or PR-AUC instead.
- **Optimizing the wrong metric.** If you tune your model to maximize ROC-AUC but your decision depends on top-k enrichment, the model may not be optimal for your actual use case.
- **Ignoring calibration.** A model that ranks well but produces poorly calibrated probabilities can mislead decision-making. If you use predicted probabilities to set thresholds or allocate resources, calibration matters.
- **Comparing metrics across different datasets.** An R² of 0.8 on an easy dataset (narrow property range, close analogs) is not comparable to an R² of 0.8 on a hard dataset (wide property range, diverse scaffolds).

## Practical Decision Rules

1. **Choose metrics that match your decision.** If you are screening, use enrichment metrics. If you are predicting quantities, use MAE or RMSE. If you are classifying with imbalanced data, use PR-AUC or F1.
2. **Report multiple metrics.** No single metric tells the whole story. Report at least two complementary metrics.
3. **Always report the baseline metric** (dummy model performance) for context.
4. **Check calibration** if predicted probabilities will be used for decision-making.
5. **Be explicit about the threshold** for classification metrics. Precision and recall depend on the chosen threshold.

## Mini-Summary

There is no universally best metric — only metrics aligned or misaligned with the real use case. Regression metrics (MAE, RMSE, R²), classification metrics (precision, recall, F1, ROC-AUC, PR-AUC), and ranking metrics (enrichment) each answer different questions. Choose the metric that matches the decision your model will support, and always report baselines for context.

## Exercises and Thought Questions

1. A model achieves ROC-AUC = 0.95 but precision at the top 100 predictions is only 10%. Is this a good model for virtual screening? Why or why not?

2. You are predicting solubility for formulation design, where errors greater than 1 log unit are unacceptable. Which metric would you prioritize: MAE, RMSE, or R²?

3. Your dataset has 2% actives and 98% inactives. Compare the informativeness of accuracy, ROC-AUC, and PR-AUC for this dataset.

4. Two models have the same ROC-AUC but different calibration. When does calibration matter? When can you ignore it?

5. Design a metric that captures the cost of false positives (wasted experiments at $10,000 each) and false negatives (missed drug candidates worth $1M each). How would this metric change your model selection?
