# Appendix F — Metrics Cheat Sheet

---

## Regression Metrics

| Metric | What It Measures | When to Use | Watch Out For |
|---|---|---|---|
| **MAE** | Average absolute error | General regression; robust to outliers | Does not penalize large errors heavily |
| **RMSE** | Root mean squared error | When large errors are costly | Sensitive to outliers |
| **R²** | Fraction of variance explained | Comparing models on the same dataset | Misleading on small or non-representative test sets |
| **Spearman ρ** | Rank correlation | When ranking matters more than absolute values | Ignores magnitude of errors |

## Classification Metrics

| Metric | What It Measures | When to Use | Watch Out For |
|---|---|---|---|
| **Accuracy** | Fraction correct | Balanced classes only | Misleading for imbalanced data |
| **Precision** | Fraction of predicted positives that are true | When false positives are costly | Ignores false negatives |
| **Recall** | Fraction of true positives found | When false negatives are costly | Ignores false positives |
| **F1** | Harmonic mean of precision and recall | Balanced trade-off | Depends on threshold |
| **ROC-AUC** | Ranking quality across all thresholds | General classifier comparison | Optimistic for imbalanced data |
| **PR-AUC** | Ranking quality for the positive class | Imbalanced data (rare actives) | Harder to interpret than ROC-AUC |
| **MCC** | Balanced measure for binary classification | Imbalanced data | Less commonly reported |

## Ranking / Screening Metrics

| Metric | What It Measures | When to Use | Watch Out For |
|---|---|---|---|
| **Enrichment factor** | Fold-enrichment of actives in top fraction | Virtual screening | Depends on the fraction chosen |
| **Top-k recall** | Fraction of actives in top k predictions | Fixed screening budget | Depends on k |
| **BEDROC** | Early enrichment with exponential weighting | Virtual screening with emphasis on top ranks | Parameter-dependent |

## Choosing the Right Metric

1. **What decision does the model support?**
   - Quantitative prediction → MAE or RMSE
   - Ranking for screening → enrichment or top-k recall
   - Binary decision → precision, recall, F1
   - General comparison → R² (regression) or ROC-AUC (classification)

2. **Is the data imbalanced?**
   - Yes → PR-AUC, F1, enrichment (not accuracy or ROC-AUC)
   - No → accuracy, ROC-AUC are acceptable

3. **What is the cost of errors?**
   - False positives costly → optimize precision
   - False negatives costly → optimize recall
   - Both costly → optimize F1 or choose a threshold that balances both
