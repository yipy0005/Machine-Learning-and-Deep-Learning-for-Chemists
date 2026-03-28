# Chapter 11 — Linear Models: Simple Does Not Mean Useless

---

## Chemical Motivation

The simplest quantitative model a chemist might build is a linear one: "solubility decreases as logP increases." This is a statement about a weighted, additive relationship between a molecular property and a descriptor. Linear models formalize this reasoning, and despite their simplicity, they remain among the most useful tools in chemistry ML.

Linear models are transparent. You can inspect every coefficient and understand exactly how each feature contributes to the prediction. In a field where interpretability matters — where a chemist needs to understand *why* a model predicts what it does — this transparency is invaluable.

## Core Concept

A linear model predicts the target as a weighted sum of input features. Each feature (descriptor, fingerprint bit) receives a coefficient that represents its contribution to the prediction. The model learns these coefficients from the training data.

Think of it as a recipe. Each ingredient (feature) contributes a certain amount to the final dish (prediction), and the recipe (model) specifies how much of each ingredient to use. The contribution of each ingredient is independent of the others — adding more salt does not change how much sugar contributes.

### Regression vs. Classification

In linear regression, the output is a continuous number — the weighted sum of features directly gives the prediction. This is appropriate for predicting quantities like solubility, melting point, or yield.

In logistic regression (the classification counterpart), the weighted sum is passed through a function that converts it to a probability between 0 and 1. This is appropriate for predicting categories like active/inactive or toxic/non-toxic.

### Feature Coefficients and Interpretability

The coefficients of a linear model are directly interpretable. A positive coefficient for cLogP in a solubility model means that increasing lipophilicity is associated with decreasing solubility (if solubility is the negative target). A large coefficient means the feature has a strong influence; a small coefficient means it has a weak influence.

This interpretability is the linear model's greatest strength. A chemist can examine the coefficients and ask: "Does this make chemical sense?" If the model assigns a large positive coefficient to molecular weight for predicting membrane permeability, the chemist can flag this as suspicious — large molecules generally have lower permeability.

### Regularization

When you have many features (hundreds of descriptors) and relatively few compounds, a linear model can overfit — it finds coefficients that perfectly fit the training data but fail on new data. This is like fitting a polynomial through every data point in a calibration curve: the fit is perfect, but the curve oscillates wildly between points.

Regularization prevents this by penalizing large coefficients. The model is encouraged to find a solution where most coefficients are small, using only the features that truly matter. There are two common forms:

- **Ridge regularization** shrinks all coefficients toward zero but keeps all features in the model. It is like adding a damping term that prevents any single feature from dominating.
- **Lasso regularization** can shrink some coefficients exactly to zero, effectively removing those features from the model. It performs automatic feature selection.

Think of regularization as a purity constraint on your model. Just as you would not add unnecessary reagents to a reaction, regularization prevents the model from using unnecessary features.

## Chemist-Friendly Intuition

A linear model is essentially a weighted property equation. In the early days of QSAR, Hansch and Fujita proposed that biological activity could be expressed as a linear combination of physicochemical properties — lipophilicity, electronic effects, and steric effects. This Hansch equation is a linear model.

The assumption behind a linear model is that effects combine additively. Increasing logP by one unit always changes the predicted solubility by the same amount, regardless of the molecule's other properties. This is often a reasonable first approximation, especially for well-behaved descriptor spaces.

When the assumption breaks down — when the effect of one feature depends on the value of another (an interaction effect) — linear models will underperform. But they will underperform transparently, and the residuals (prediction errors) will reveal where the linear assumption fails.

## Concrete Chemical Example

You have 1,000 compounds with measured aqueous solubility and 50 physicochemical descriptors. You fit a ridge regression model:

The model learns coefficients like:
- cLogP: -0.8 (higher lipophilicity → lower solubility, as expected)
- TPSA: +0.3 (higher polar surface area → higher solubility, as expected)
- Molecular weight: -0.2 (larger molecules → slightly lower solubility)
- Number of rotatable bonds: -0.1 (more flexibility → slightly lower solubility)

A chemist can read these coefficients and immediately verify that they make chemical sense. The model is not a black box — it is a quantitative version of the chemist's own intuition.

If the model predicts poorly for a particular compound, the chemist can examine the residual and ask: "What is special about this compound that the linear model misses?" Perhaps it forms an intramolecular hydrogen bond that dramatically changes its effective polarity — an interaction effect that a linear model cannot capture.

## What Can Go Wrong

- **Assuming linearity when the relationship is nonlinear.** If the true relationship between features and target involves interactions or thresholds, a linear model will systematically mispredict. Check residuals for patterns.

- **Ignoring collinearity.** If two features are highly correlated (e.g., molecular weight and number of heavy atoms), their individual coefficients become unstable — small changes in the data can flip their signs. Regularization helps, but awareness of collinearity is important for interpretation.

- **Over-interpreting coefficients.** A coefficient reflects the feature's contribution *in the context of all other features in the model*. Adding or removing features can change coefficients substantially. Do not interpret a single coefficient in isolation.

- **Forgetting to scale features.** Linear models are sensitive to feature scale. If molecular weight ranges from 100 to 800 and TPSA ranges from 0 to 200, the model will assign artificially small coefficients to molecular weight. Always scale features before fitting.

## Practical Decision Rules

1. **Use linear models as a first-line approach** for descriptor-based prediction tasks, especially when interpretability is important.

2. **Always use regularization** (ridge or lasso) when you have more than a handful of features. The regularization strength should be chosen by cross-validation.

3. **Scale your features** to zero mean and unit variance before fitting.

4. **Examine coefficients for chemical plausibility.** If a coefficient does not make chemical sense, investigate — it may reveal a data issue or a confound.

5. **Use linear models as a diagnostic tool.** Even if you ultimately use a more complex model, the linear model's coefficients and residuals provide valuable insight into the data.

## Mini-Summary

Linear models predict the target as a weighted sum of features, with each coefficient representing a feature's contribution. They are transparent, fast, and often surprisingly competitive on structured chemical features. Their main limitation is the assumption of additive, linear relationships — when interactions matter, nonlinear models will do better. But linear models remain the best starting point for interpretable, descriptor-based chemistry ML.

## Exercises and Thought Questions

1. Fit a ridge regression model to predict solubility from five descriptors (MW, cLogP, TPSA, HBD, HBA). Examine the coefficients. Do they match your chemical intuition?

2. You fit a linear model and find that the coefficient for "number of fluorine atoms" is large and positive for predicting metabolic stability. Is this chemically reasonable? What might explain it?

3. Compare ridge and lasso regression on the same dataset. Which produces a sparser model? Which has better predictive performance? When would you prefer one over the other?

4. A linear model achieves R² = 0.7 on your dataset. A random forest achieves R² = 0.85. What does the gap tell you about the nature of the structure–property relationship?

5. Why might a linear model perform well on a small dataset (100 compounds) but poorly on a large, diverse dataset (10,000 compounds)?
