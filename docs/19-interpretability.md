# Chapter 19 — Interpretability, Explanations, and False Comfort

---

## Chemical Motivation

Chemists do not just want predictions — they want understanding. When a model predicts that a compound will be toxic, the chemist asks: "Why? What structural feature is responsible? Can I modify the molecule to remove the liability?" Interpretability tools promise to answer these questions, but they must be used with care. An explanation that looks convincing but is wrong is worse than no explanation at all.

## Core Concept

### Feature Importance

Many models provide feature importance scores — numbers indicating how much each feature contributes to predictions. Random forests compute importance based on how much each feature improves splits. Linear models provide coefficients. These scores can guide chemical intuition, but they have important limitations.

Feature importance is a property of the model, not of the chemistry. If two features are correlated (e.g., cLogP and number of aromatic rings), the model may assign importance to one and ignore the other, even though both are chemically relevant. The assignment is arbitrary and can change with small perturbations of the data.

### SHAP-Style Intuition

SHAP (SHapley Additive exPlanations) values provide per-prediction, per-feature explanations. For each prediction, SHAP tells you how much each feature pushed the prediction up or down relative to the average.

Think of SHAP as a decomposition of the prediction into contributions. If the model predicts high toxicity for a compound, SHAP might show that the presence of a nitro group contributed +0.5 to the toxicity score, while high molecular weight contributed -0.2. This decomposition is useful for generating hypotheses about which structural features drive the prediction.

However, SHAP values are explanations of the model's reasoning, not explanations of the chemistry. If the model has learned a spurious correlation (e.g., a particular substructure correlates with activity in the training set by coincidence), SHAP will faithfully report that substructure as important — even though it is not causally related to activity.

### Substructure Attribution

For molecular models, a common interpretability approach is to highlight which atoms or substructures contribute most to the prediction. This produces colored molecular diagrams where red atoms drive the prediction up and blue atoms drive it down.

These visualizations are compelling and intuitive for chemists. But they can also be misleading:
- The attribution may highlight a substructure that correlates with the target in the training data but is not causally responsible.
- Different attribution methods (gradient-based, attention-based, SHAP-based) can give different results for the same molecule and model.
- The attribution is specific to the model, not to the chemistry. A different model trained on the same data may highlight different substructures.

### Counterfactual Examples

A counterfactual explanation asks: "What is the smallest change to this molecule that would change the prediction?" For example, "If you replaced the chlorine with a fluorine, the model would predict the compound as non-toxic."

Counterfactuals are actionable — they suggest specific modifications. But they are also model-dependent and may not reflect real chemistry. The suggested modification might be synthetically impractical or might change other properties in undesirable ways.

## Chemist-Friendly Intuition

Interpretability tools are like analytical instruments — they provide data that must be interpreted by an expert. An NMR spectrum does not interpret itself; a trained chemist reads it, considers alternatives, and draws conclusions. Similarly, a SHAP plot or an atom attribution map does not interpret itself. It provides evidence that the chemist must evaluate critically.

The danger is false comfort. A colorful attribution map that highlights a known toxicophore feels reassuring — "The model understands the chemistry!" But the model may have learned the correlation for the wrong reason, or the attribution may be unstable across similar molecules.

## Concrete Chemical Example

You build a model to predict mutagenicity (Ames test) and use SHAP to explain predictions. For a compound predicted as mutagenic, SHAP highlights the nitro group as the primary driver. This is chemically plausible — nitro groups are known mutagens.

But for another compound, SHAP highlights a methyl group as driving the mutagenicity prediction. This is chemically implausible. Investigation reveals that in the training data, the methyl group happens to co-occur with a mutagenic substructure that the model did not learn to recognize directly.

This example illustrates the core challenge: interpretability tools explain the model, not the chemistry. When the model's reasoning aligns with chemical knowledge, the explanation is useful. When it does not, the explanation reveals a model limitation.

## What Can Go Wrong

- **Treating explanations as causal.** SHAP values and feature importances show correlations in the model, not causal mechanisms. A feature may be important because it correlates with the true driver, not because it is the true driver.
- **Instability of explanations.** Small changes to the training data or model hyperparameters can produce very different explanations. If the explanation is not robust, it should not be trusted.
- **Confirmation bias.** Chemists may accept explanations that match their expectations and dismiss those that do not, without critically evaluating either.
- **Over-reliance on visualization.** A colorful atom attribution map looks authoritative but may be misleading. Always cross-reference with chemical knowledge and independent evidence.

## Practical Decision Rules

1. **Use interpretability tools to generate hypotheses, not to confirm them.** An attribution that highlights a substructure is a starting point for investigation, not a conclusion.
2. **Check stability.** Retrain the model with different random seeds or slightly different data and see if the explanations change. Stable explanations are more trustworthy.
3. **Cross-reference with chemistry.** If the explanation contradicts chemical knowledge, investigate the model, not the chemistry.
4. **Use multiple explanation methods.** If gradient-based attribution, SHAP, and attention all highlight the same substructure, the evidence is stronger.
5. **Be honest about limitations.** Report that explanations are model-dependent and may not reflect causal chemistry.

## Mini-Summary

Interpretability tools — feature importance, SHAP values, substructure attribution, counterfactuals — can help chemists understand model predictions and generate hypotheses. But they explain the model's reasoning, not the underlying chemistry. Explanations can be unstable, misleading, or reflect spurious correlations. Use them as analytical tools that require expert interpretation, not as automatic truth generators.

## Exercises and Thought Questions

1. Train two different models (random forest and neural network) on the same dataset and compare their feature importances. Do they agree? What does disagreement tell you?

2. A SHAP analysis highlights molecular weight as the most important feature for predicting binding affinity. Is this chemically meaningful, or might it be a proxy for something else?

3. You generate atom attribution maps for 10 compounds predicted as toxic. For 7 of them, the attribution highlights known toxicophores. For 3, it highlights chemically implausible features. How would you report these results?

4. A counterfactual explanation suggests that replacing a phenyl ring with a cyclohexyl ring would make a compound non-toxic. How would you evaluate this suggestion before acting on it?

5. When is interpretability more important than predictive accuracy? When is accuracy more important than interpretability?
