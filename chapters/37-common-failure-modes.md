# Chapter 37 — Common Failure Modes in Chemistry Machine Learning

---

## Chemical Motivation

Many chemistry ML projects fail not because the model is weak but because the scientific design is weak. Understanding common failure modes — and how to avoid them — is as important as understanding the models themselves. This chapter is a field guide to the mistakes that derail chemistry ML projects.

## Core Concept

### Leakage
Data leakage occurs when information from the test set contaminates the training process. This inflates performance and produces models that fail in deployment. Common forms: feature scaling on the full dataset, target-derived features, temporal leakage, and duplicate compounds across splits.

### Bad Splits
Random splits in chemistry allow close analogs in both training and test sets, inflating performance (Chapter 16). Scaffold splits, time splits, and series splits provide more honest evaluation.

### Confounded Labels
When the target variable correlates with a confound (batch, plate position, assay date), the model may learn the confound rather than the chemistry. This is like a model that predicts activity based on which lab ran the assay rather than on molecular structure.

### Overclaiming from Benchmarks
Performance on a benchmark dataset does not guarantee performance on your data. Benchmarks have specific characteristics (chemical space, noise level, split strategy) that may not match your problem.

### Benchmark Overfitting
When the community repeatedly optimizes models on the same benchmarks, the reported improvements may reflect overfitting to the benchmark rather than genuine advances. This is the ML equivalent of optimizing a reaction for one specific substrate.

### Poor Curation
Uncurated data — with duplicates, mislabeled compounds, inconsistent units, or mixed assay protocols — produces unreliable models. Data curation is unglamorous but essential.

### Shortcut Learning
Models may learn shortcuts — spurious correlations that work on the training data but fail on new data. For example, a model might learn that compounds with a specific vendor code tend to be active, because that vendor supplied the active compounds in the training set.

### Ignored Uncertainty
Reporting predictions without uncertainty estimates, or deploying models outside their applicability domain, leads to overconfident decisions based on unreliable predictions.

## Practical Decision Rules

1. **Audit for leakage** before trusting any result. Check that no test information influenced training.
2. **Use chemistry-aware splits.** Scaffold splits are the minimum standard for molecular property prediction.
3. **Check for confounds.** Examine whether the target correlates with non-chemical variables (batch, date, source).
4. **Curate data carefully.** Remove duplicates, standardize units, and document exclusions.
5. **Report uncertainty and applicability domain.** Flag predictions outside the model's reliable range.
6. **Compare to external data.** Performance on your own data is the ultimate test, not performance on a benchmark.

## Mini-Summary

The most common failures in chemistry ML are not algorithmic — they are scientific. Leakage, bad splits, confounded labels, poor curation, and ignored uncertainty are the enemies of reliable models. Avoiding these failures requires the same experimental discipline that chemists apply to laboratory work. A well-designed simple model beats a poorly designed complex one every time.

## Exercises and Thought Questions

1. You discover that your model's test set contains 50 compounds that are exact duplicates of training compounds. How does this affect your reported performance?
2. A model achieves R² = 0.95 on a benchmark but R² = 0.50 on your internal data. List five possible explanations.
3. Your dataset was collected over 5 years. You notice that compounds tested in 2023 have systematically higher activity than those tested in 2019. Is this a real trend or a confound?
4. Design a checklist for auditing a chemistry ML project for common failure modes.
5. A colleague's model uses "assay plate ID" as a feature and achieves excellent performance. What is the problem?
