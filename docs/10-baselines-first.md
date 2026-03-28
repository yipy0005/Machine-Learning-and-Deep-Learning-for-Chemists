# Chapter 10 — Baselines First: The Most Underrated Scientific Habit

## Part III — Classical Machine Learning Every Chemist Should Know

---

## Chemical Motivation

No chemist would publish a reaction yield without a control experiment. No analytical chemist would report a measurement without a blank. Yet in machine learning, researchers routinely report model performance without comparing to simple baselines. This is the equivalent of claiming a catalyst is effective without running the uncatalyzed reaction.

A baseline is the control experiment of machine learning. It tells you how well a simple, transparent method performs on your data, establishing a floor that any more complex model must beat to justify its complexity.

## Core Concept

A baseline model is a simple, often trivial model that provides a reference point for evaluating more sophisticated approaches. Baselines answer the question: "How much of this problem can be solved without any cleverness at all?"

### Types of Baselines

**Dummy baselines** — the simplest possible predictions:
- For regression: predict the mean (or median) of the training labels for every compound. This tells you how much of the variance is explained by the target distribution alone.
- For classification: predict the majority class for every compound. If 90% of your compounds are inactive, a dummy classifier that always predicts "inactive" achieves 90% accuracy — a number that sounds impressive but is meaningless.

**Nearest-neighbor baselines** — predict based on the most similar training example:
- For a new compound, find the most similar compound in the training set (using fingerprint similarity) and predict its label. This tests whether simple chemical similarity is sufficient.

**Linear baselines** — fit a linear model on descriptors or fingerprints:
- Linear regression or logistic regression with regularization. This tests whether the relationship between features and target is approximately linear.

**Tree baselines** — fit a single decision tree or a small random forest:
- This tests whether simple nonlinear rules capture the pattern.

### Why Baselines Matter

Baselines serve several critical functions:

1. **They calibrate expectations.** If a nearest-neighbor model achieves R² = 0.75 on your solubility data, then a deep neural network that achieves R² = 0.78 is a marginal improvement, not a breakthrough.

2. **They reveal data quality issues.** If no model — not even a complex one — beats the dummy baseline, the data may be too noisy, the labels too unreliable, or the representation too uninformative.

3. **They prevent wasted effort.** If a random forest on fingerprints achieves 90% of the performance of a graph neural network at 1% of the computational cost, the GNN may not be worth the investment.

4. **They enforce intellectual honesty.** Reporting only the best model without baselines is cherry-picking. It is the ML equivalent of reporting only the best yield from a reaction screen without mentioning the failures.

## Chemist-Friendly Intuition

Think of baselines as the uncatalyzed reaction rate. Before you claim a catalyst is effective, you need to know the background rate. Before you claim a model is effective, you need to know the baseline performance.

In medicinal chemistry, the simplest "model" is often a chemist's intuition: "compounds similar to known actives are likely active." A nearest-neighbor baseline formalizes this intuition. If a sophisticated model cannot beat the nearest-neighbor baseline, it has not learned anything beyond what simple similarity provides.

Similarly, Lipinski's Rule of Five is a baseline for oral bioavailability prediction. Any ML model for oral bioavailability should be compared to this simple, interpretable rule. If the ML model is only marginally better, the added complexity may not be justified.

## Concrete Chemical Example

You are building a model to predict aqueous solubility from molecular descriptors. Before training any sophisticated model, you establish baselines:

1. **Dummy baseline:** Predict the mean training solubility (-3.2 log mol/L) for every compound. RMSE = 2.1 log units. This is terrible, but it is your floor.

2. **Nearest-neighbor baseline:** For each test compound, find the most similar training compound (by Tanimoto similarity on Morgan fingerprints) and predict its solubility. RMSE = 1.3 log units. Simple similarity already explains a lot.

3. **Linear baseline:** Fit a ridge regression on 200 RDKit descriptors. RMSE = 0.95 log units. A linear model on descriptors does surprisingly well.

4. **Random forest baseline:** Fit a random forest on the same descriptors. RMSE = 0.82 log units. Nonlinear interactions help modestly.

Now you train a graph neural network. It achieves RMSE = 0.78 log units. Is this a significant improvement over the random forest? Perhaps — but the improvement is small, and the GNN is far more expensive to train and harder to interpret. The baselines give you the context to make an informed decision.

## What Can Go Wrong

- **Skipping baselines entirely.** This is the most common mistake. Without baselines, you cannot interpret model performance. An R² of 0.8 means nothing without knowing what a simple model achieves.

- **Using weak baselines to inflate the apparent improvement.** Comparing a neural network to a dummy baseline makes the neural network look impressive. Comparing it to a well-tuned random forest gives a more honest picture.

- **Ignoring the cost of complexity.** A model that is 2% better but 100x more expensive to train and deploy may not be the right choice. Baselines help quantify the cost-benefit trade-off.

- **Treating deep learning as the default.** Deep learning should not be the first model you try. It should be the model you try after simpler approaches have been exhausted or shown to be insufficient.

## Practical Decision Rules

1. **Always start with a dummy baseline.** It takes seconds to compute and establishes the absolute floor.

2. **Always include a nearest-neighbor baseline** for chemistry problems. It tests the similarity principle directly.

3. **Always include a linear model baseline.** It tests whether the relationship is approximately linear and provides an interpretable reference.

4. **Always include a tree-based baseline** (random forest or gradient boosting). These are strong general-purpose models that are hard to beat on tabular data.

5. **Report all baselines alongside your final model.** This is scientific honesty. It lets readers judge whether the improvement justifies the complexity.

## Mini-Summary

A baseline is the control experiment of machine learning. It establishes the performance floor that any more complex model must beat. Dummy baselines, nearest-neighbor models, linear models, and tree-based models are the essential baselines for chemistry ML. Skipping baselines is like skipping controls — it makes your results uninterpretable. Always start simple, and let the data tell you when complexity is justified.

## Exercises and Thought Questions

1. You build a neural network for toxicity prediction that achieves 85% accuracy. The dataset is 85% non-toxic. What does this tell you? What baseline should you have computed first?

2. A paper reports that their GNN achieves state-of-the-art performance on a molecular property benchmark. The paper does not include a random forest baseline. How would you evaluate this claim?

3. For a regression task with 500 training compounds, rank the following models from simplest to most complex: GNN, random forest, linear regression, nearest neighbor, dummy mean predictor. In what order would you try them?

4. Your nearest-neighbor baseline achieves R² = 0.9 on a solubility prediction task. What does this tell you about the dataset? What might it imply about the difficulty of the task?

5. When might a complex model be justified even if it only marginally outperforms a simple baseline?
