# Chapter 18 — Overfitting, Underfitting, and Generalization

---

## Chemical Motivation

A chemist who memorizes every compound in a training set and their activities has not learned SAR — they have memorized a table. When shown a new compound, they cannot predict its activity because they never learned the underlying pattern. This is overfitting: the model has memorized the training data rather than learning generalizable chemistry.

Conversely, a chemist who insists that "all compounds have the same activity" has learned nothing at all. This is underfitting: the model is too simple to capture any pattern.

The goal of machine learning is generalization — learning patterns that transfer to new, unseen compounds. This chapter explains how to recognize and prevent the two failure modes that undermine generalization.

## Core Concept

### Overfitting: Memorizing Noise

An overfit model performs excellently on training data but poorly on new data. It has learned the specific quirks of the training set — including noise, measurement errors, and coincidental correlations — rather than the underlying chemical pattern.

Signs of overfitting:
- Training performance is much better than validation/test performance
- Performance degrades sharply on scaffold splits compared to random splits
- The model is very complex relative to the amount of data

### Underfitting: Missing the Pattern

An underfit model performs poorly on both training and test data. It is too simple to capture the relationship between features and target.

Signs of underfitting:
- Training performance is poor
- Adding more data does not help
- The model is very simple relative to the complexity of the data

### The Complexity–Data Balance

The relationship between model complexity and data volume is central to generalization. A complex model (deep neural network with millions of parameters) needs a lot of data to learn meaningful patterns. With too little data, it memorizes. A simple model (linear regression with five features) needs less data but may miss complex patterns.

Think of it as a titration. Too little model complexity (too little titrant) and you undershoot — the model cannot capture the pattern. Too much complexity (too much titrant) and you overshoot — the model memorizes noise. The goal is to find the equivalence point where complexity matches the information content of the data.

### Regularization

Regularization is the primary tool for preventing overfitting. It adds a penalty for model complexity, encouraging the model to find simpler solutions that generalize better.

In chemical terms, regularization is like Occam's razor applied to models: among models that fit the data equally well, prefer the simplest one. A model that explains solubility using three descriptors is preferable to one that uses 300, unless the extra descriptors demonstrably improve predictions on new data.

Common regularization techniques:
- **Weight penalties** (ridge, lasso) — penalize large model parameters
- **Dropout** (neural networks) — randomly disable parts of the network during training, forcing it to learn redundant representations
- **Early stopping** — stop training before the model has fully memorized the training data
- **Ensemble methods** — combine multiple models to average out individual overfitting

## Chemist-Friendly Intuition

Overfitting is like a chemist who has worked on one chemical series for years and can predict activity within that series perfectly but fails completely on a new scaffold. They have memorized the specific SAR of their series, not the general principles of molecular recognition.

Underfitting is like a first-year student who knows that "lipophilic compounds tend to be more active" but cannot make more specific predictions. They have learned a crude rule but not the nuances.

Generalization is like an experienced medicinal chemist who can look at a new scaffold and make reasonable predictions based on transferable principles — understanding of pharmacophores, binding interactions, and property–activity relationships that apply across chemical series.

The goal of ML is to produce models that behave like the experienced chemist, not the memorizer or the novice.

## Concrete Chemical Example

You train a neural network to predict hERG inhibition from molecular descriptors. With 500 training compounds and a network with 100,000 parameters:

- Training R² = 0.98 (nearly perfect fit)
- Test R² = 0.45 (poor generalization)

The model has memorized the training data. You apply regularization:

1. **Reduce network size** to 1,000 parameters → Training R² = 0.75, Test R² = 0.65
2. **Add dropout** (randomly disable 30% of neurons during training) → Training R² = 0.72, Test R² = 0.68
3. **Apply early stopping** (stop training when validation performance plateaus) → Training R² = 0.70, Test R² = 0.69

The regularized model has lower training performance but much better test performance. It has traded memorization for generalization.

## What Can Go Wrong

- **Ignoring the train-test gap.** If training performance is much better than test performance, the model is overfitting. This gap is the most important diagnostic.
- **Adding complexity without more data.** Switching from a random forest to a deep neural network without increasing the dataset size often makes overfitting worse, not better.
- **Under-regularizing.** Regularization strength is a hyperparameter that must be tuned. Too little regularization allows overfitting; too much causes underfitting.
- **Confusing memorization with learning.** A model that achieves R² = 0.99 on training data is almost certainly overfitting unless the dataset is very large and very clean.

## Practical Decision Rules

1. **Always compare training and test performance.** A large gap indicates overfitting.
2. **Start with simple models and increase complexity only if needed.** This naturally prevents overfitting.
3. **Use regularization for any model with many parameters.** The regularization strength should be chosen by cross-validation.
4. **Use early stopping for neural networks.** Monitor validation performance during training and stop when it plateaus.
5. **Match model complexity to data size.** With 100 compounds, use a simple model. With 100,000 compounds, a complex model may be justified.

## Mini-Summary

Overfitting (memorizing training data) and underfitting (failing to capture patterns) are the two failure modes of ML models. Generalization — the ability to predict accurately on new data — requires balancing model complexity with data volume. Regularization, early stopping, and disciplined evaluation are the tools for achieving this balance. A model that memorizes known analogs is not the same as a model that understands transferable SAR.

## Exercises and Thought Questions

1. You train a model with training R² = 0.95 and test R² = 0.50. Is this overfitting or underfitting? What would you try to fix it?

2. You increase your training set from 500 to 5,000 compounds. The train-test gap shrinks from 0.40 to 0.10. Why does more data help with overfitting?

3. A colleague argues that a model with training R² = 0.99 is excellent. What question would you ask before agreeing?

4. Compare the effect of regularization on a linear model vs. a neural network. Which benefits more? Why?

5. When might overfitting be acceptable? (Hint: think about the difference between prediction and interpretation.)
