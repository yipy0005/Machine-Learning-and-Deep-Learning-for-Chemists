# Appendix B — Minimal Statistics for Model Evaluation

---

## Distributions

A distribution describes how values are spread. The distribution of solubility values in your dataset might be roughly bell-shaped (normal), skewed toward low solubility, or bimodal. Understanding the distribution of your target variable helps you choose appropriate models and metrics.

## Confidence Intervals

A confidence interval quantifies the uncertainty in an estimate. If your model's test R² is 0.75 ± 0.05 (95% confidence interval), it means the true performance is likely between 0.70 and 0.80. Wider intervals indicate more uncertainty.

For ML, confidence intervals come from:
- Multiple cross-validation folds
- Multiple random seeds
- Bootstrapping (resampling the test set)

Always report confidence intervals. A single number without uncertainty is incomplete.

## Calibration

A calibrated model produces probabilities that match observed frequencies. If the model predicts 80% probability of activity for a group of compounds, approximately 80% should actually be active.

Calibration is assessed by plotting predicted probability vs. observed frequency (a calibration plot or reliability diagram). A perfectly calibrated model falls on the diagonal.

## Uncertainty vs. Error

- **Error** is the difference between prediction and truth for a specific example.
- **Uncertainty** is the model's estimate of how large the error might be, before knowing the truth.

A good uncertainty estimate is one where high-uncertainty predictions have large errors and low-uncertainty predictions have small errors. This is assessed by plotting uncertainty vs. absolute error.

## Significance vs. Usefulness

Statistical significance tells you whether a difference is likely real (not due to chance). Practical usefulness tells you whether the difference matters for your application.

A model improvement from R² = 0.80 to R² = 0.81 might be statistically significant (with enough data) but practically useless (the improvement is too small to change any decision).

Conversely, an improvement from R² = 0.50 to R² = 0.70 is both significant and useful — it represents a meaningful advance in predictive capability.

Always ask both questions: "Is this difference real?" and "Does this difference matter?"
