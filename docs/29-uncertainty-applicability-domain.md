# Chapter 29 — Uncertainty and Applicability Domain

---

## Chemical Motivation

A prediction without uncertainty is like a measurement without error bars — it looks precise but may be misleading. When a model predicts that a compound has a solubility of -3.2 log units, the chemist needs to know: is this ±0.3 or ±2.0? The answer determines whether the prediction is useful for decision-making or merely decorative.

Uncertainty quantification is one of the most important and most neglected topics in chemistry ML. A model that knows when it does not know is far more valuable than one that always predicts confidently, even when it should not.

## Core Concept

### Two Types of Uncertainty

**Aleatoric uncertainty** (data uncertainty) arises from noise in the measurements. Even with a perfect model, predictions would be uncertain because the underlying data is noisy. Two measurements of the same compound's solubility may differ by 0.5 log units — this irreducible noise sets a floor on prediction uncertainty.

**Epistemic uncertainty** (model uncertainty) arises from the model's lack of knowledge. When the model encounters a compound unlike anything in its training data, it should be uncertain — not because the measurement is noisy, but because the model has no basis for a confident prediction.

In chemical terms:
- Aleatoric uncertainty is like the reproducibility of an assay — it exists even for well-characterized compounds.
- Epistemic uncertainty is like the reliability of an extrapolation — it increases as you move away from known chemistry.

### Confidence vs. Calibration

A model's predicted confidence should match its actual accuracy. If the model says "I am 90% confident this compound is active," then among all compounds where the model is 90% confident, approximately 90% should actually be active. This is calibration.

Many models are poorly calibrated — they are overconfident (predicting high confidence even when wrong) or underconfident (predicting low confidence even when right). Calibration can be assessed and corrected post-hoc.

### Applicability Domain

The applicability domain is the region of chemical space where the model's predictions are reliable. Outside this domain, predictions should be treated with caution or rejected entirely.

The applicability domain is defined by the training data. A model trained on drug-like molecules has an applicability domain that covers drug-like chemical space. Predictions for polymers, organometallics, or natural products may fall outside this domain.

Practical approaches to defining the applicability domain include:
- **Distance to nearest training example** — if the nearest training compound is very dissimilar, the prediction is out of domain.
- **Ensemble disagreement** — if multiple models disagree on the prediction, the compound may be out of domain.
- **Density estimation** — if the compound falls in a low-density region of the training data distribution, it may be out of domain.

## Chemist-Friendly Intuition

Think of uncertainty as the error bars on a prediction. Just as you would not trust a measurement without error bars, you should not trust a prediction without uncertainty.

The applicability domain is like the valid range of a calibration curve. Within the range, predictions are reliable. Outside the range, you are extrapolating, and the uncertainty grows. A good model tells you when you are extrapolating.

## Concrete Chemical Example

You build a solubility prediction model and deploy it to screen 10,000 virtual compounds. For each compound, the model provides:
- A predicted solubility value
- An uncertainty estimate (from an ensemble of 10 models)

You find:
- 7,000 compounds have low uncertainty (ensemble standard deviation < 0.5 log units). These are within the applicability domain, and the predictions are reliable.
- 2,000 compounds have moderate uncertainty (0.5–1.0 log units). These are near the boundary of the applicability domain. Predictions are useful but should be treated cautiously.
- 1,000 compounds have high uncertainty (> 1.0 log units). These are outside the applicability domain. Predictions are unreliable and should not be used for decision-making.

By flagging high-uncertainty predictions, you avoid making decisions based on unreliable information — and you identify compounds that might need experimental measurement.

## What Can Go Wrong

- **Ignoring uncertainty entirely.** Treating all predictions as equally reliable leads to poor decisions for out-of-domain compounds.
- **Overconfident models.** Many models (especially neural networks) produce overconfident predictions. Always check calibration.
- **Confusing aleatoric and epistemic uncertainty.** Aleatoric uncertainty cannot be reduced by collecting more data (it is inherent in the measurement). Epistemic uncertainty can be reduced by expanding the training set.
- **Rigid applicability domain boundaries.** The applicability domain is not a sharp boundary — it is a gradient. Predictions become less reliable gradually as you move away from the training data.

## Practical Decision Rules

1. **Always provide uncertainty estimates** alongside predictions. Ensemble methods (training multiple models and measuring their disagreement) are the simplest and most reliable approach.
2. **Check calibration.** Plot predicted confidence against actual accuracy and correct if necessary.
3. **Define an applicability domain** based on distance to training data, ensemble disagreement, or density estimation.
4. **Flag out-of-domain predictions** rather than reporting them as reliable.
5. **Use uncertainty to guide experimental priorities.** Compounds with high uncertainty and high predicted value are the most informative to test experimentally.

## Mini-Summary

A useful chemistry model should not only predict but also signal when its prediction should be treated cautiously. Uncertainty arises from noisy measurements (aleatoric) and from lack of knowledge (epistemic). The applicability domain defines where predictions are reliable. Ensemble methods provide practical uncertainty estimates. Always report uncertainty alongside predictions, and use it to guide decision-making.

## Exercises and Thought Questions

1. Train an ensemble of 10 models on the same dataset. For which compounds do they agree? For which do they disagree? What characterizes the high-disagreement compounds?
2. A model predicts solubility = -4.0 ± 0.2 for compound A and solubility = -4.0 ± 1.5 for compound B. How would you treat these predictions differently?
3. Plot a calibration curve for a classification model. Is it overconfident or underconfident?
4. Define an applicability domain based on Tanimoto similarity to the nearest training compound. What threshold would you use? How would you choose it?
5. A compound falls outside the applicability domain but has a very favorable predicted property. What would you do?
