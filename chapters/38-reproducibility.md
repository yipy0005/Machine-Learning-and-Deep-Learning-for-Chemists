# Chapter 38 — Reproducibility and Reporting Standards

---

## Chemical Motivation

Science advances through reproducibility. If another group cannot reproduce your results, your contribution is uncertain at best and misleading at worst. Chemistry ML has a reproducibility problem — many published results cannot be reproduced due to missing details, unreported hyperparameters, undocumented data processing, or cherry-picked results.

## Core Concept

### Seeds and Randomness
ML models involve randomness: random weight initialization, random data shuffling, random dropout masks. Different random seeds produce different results. Reporting a single run is like reporting a single measurement — it may not be representative.

**Best practice:** Run the experiment with multiple random seeds (at least 3–5) and report mean ± standard deviation.

### Variance Across Runs
The variance across runs tells you how stable the result is. If performance varies from R² = 0.60 to R² = 0.80 across seeds, the model is unstable, and any single reported number is unreliable.

### Ablation Studies
An ablation study systematically removes components of the model to determine which ones contribute to performance. This is the ML equivalent of a control experiment — it tells you which ingredients are essential and which are dispensable.

### External Validation
Performance on your own cross-validation is necessary but not sufficient. External validation — testing on data from a different source, a different time period, or a different laboratory — provides the strongest evidence of generalization.

### Transparent Reporting
Report everything needed to reproduce the result:
- Dataset: source, size, curation steps, split strategy
- Representation: type, parameters, software version
- Model: architecture, hyperparameters, training procedure
- Evaluation: metrics, number of runs, confidence intervals
- Code: ideally, share the code and data

### Model Cards
A model card is a structured document that describes a model's intended use, performance characteristics, limitations, and training data. It is the ML equivalent of a methods section — it tells users what the model can and cannot do.

## Practical Decision Rules

1. **Report mean ± standard deviation** across multiple random seeds.
2. **Include ablation studies** to justify each component of your model.
3. **Validate externally** whenever possible.
4. **Share code and data** (or detailed descriptions if data cannot be shared).
5. **Document everything** — dataset provenance, preprocessing steps, hyperparameter choices, and software versions.

## Mini-Summary

Trustworthy chemistry ML requires reproducible workflows and honest reporting. Report multiple runs with different seeds, include ablation studies, validate externally, and share code and data. The goal is not just to produce a good number but to produce a result that others can verify and build upon.

## Exercises and Thought Questions

1. Run your model with 5 different random seeds. What is the standard deviation of the test performance? Is it larger or smaller than you expected?
2. Remove one component of your model (e.g., a feature type, a regularization technique, or a preprocessing step). How does performance change?
3. A paper reports a single test set R² without confidence intervals. How would you assess the reliability of this result?
4. Write a model card for a solubility prediction model. What information would you include?
5. You cannot share your proprietary dataset. What information about the dataset should you report to enable others to assess your results?
