# Chapter 15 — Train, Validation, and Test Sets

## Part IV — Evaluating Models Like a Scientist

---

## Chemical Motivation

A chemist would never claim a reaction works based solely on the experiment used to optimize it. You need to test it on new substrates, under slightly different conditions, in a different flask. The same principle applies to ML models: performance on the data used to build the model is meaningless. Only performance on genuinely unseen data tells you whether the model has learned something real.

This chapter introduces the most fundamental concept in model evaluation: the disciplined separation of data into training, validation, and test sets.

## Core Concept

### Three Roles for Data

- **Training set** — the data the model learns from. The model sees these examples and adjusts its parameters to fit them. This is the reaction mixture — the raw material for learning.
- **Validation set** — the data used to tune the model. You use it to choose hyperparameters (model settings), select among model variants, and decide when to stop training. This is the optimization step — you adjust conditions based on intermediate results.
- **Test set** — the data used for final evaluation. The model never sees this data during training or tuning. This is the independent replication — the experiment that confirms whether the result is real.

The critical rule is: **the test set must be touched only once, at the very end.** If you use test set performance to make any modeling decision — choosing features, tuning hyperparameters, selecting the best model — the test set is contaminated, and the reported performance is optimistically biased.

### Data Leakage

Data leakage occurs when information from the test set influences the training process. This is the ML equivalent of contaminating your blank with analyte. Common forms include:

- **Feature leakage** — using features computed from the entire dataset (including test examples) rather than from the training set alone. For example, scaling features using the mean and variance of the full dataset rather than the training set only.
- **Target leakage** — using information that would not be available at prediction time. For example, including a descriptor that is derived from the target variable.
- **Temporal leakage** — using future data to predict the past. For example, training on 2023 data and testing on 2020 data.

Leakage inflates apparent performance and produces models that fail in real deployment. It is one of the most common and most damaging errors in chemistry ML.

### Cross-Validation

When data is limited, setting aside a large validation set wastes precious training examples. Cross-validation addresses this by rotating the validation set:

1. Split the data into k folds (typically 5 or 10).
2. For each fold, train on k-1 folds and validate on the remaining fold.
3. Average the performance across all folds.

This gives a more robust estimate of model performance than a single train/validation split, because every example serves as both training and validation data (but never simultaneously).

Cross-validation is used for model selection and hyperparameter tuning. The final model is then retrained on all available training data and evaluated once on the held-out test set.

### Repeated Evaluation

ML models involve randomness — random initialization, random data shuffling, random feature subsampling (in random forests). A single run may give an optimistic or pessimistic result by chance. Repeating the evaluation (multiple cross-validation runs with different random seeds) and reporting the mean and standard deviation gives a more honest picture of model performance.

## Chemist-Friendly Intuition

Think of the three data splits as three stages of a clinical trial:

- **Training set** = Phase I (learning, dose-finding, optimization)
- **Validation set** = Phase II (testing hypotheses, refining the approach)
- **Test set** = Phase III (definitive, independent evaluation)

You would never use Phase III results to redesign the drug. Similarly, you should never use test set results to redesign the model.

Cross-validation is like running the same experiment multiple times with different batches of reagents. Each run gives a slightly different result, but the average is more reliable than any single run.

## Concrete Chemical Example

You have 2,000 compounds with measured logD values and want to build a predictive model.

1. **Set aside 400 compounds as the test set.** These compounds are locked away and will not be touched until the very end.
2. **Use the remaining 1,600 compounds for training and validation.** Apply 5-fold cross-validation: train on 1,280, validate on 320, rotate five times.
3. **Use cross-validation to choose the model.** Compare a random forest, a gradient boosting model, and a ridge regression. Select the one with the best average cross-validated performance.
4. **Use cross-validation to tune hyperparameters.** For the selected model, try different settings and choose the ones that give the best cross-validated performance.
5. **Retrain the final model on all 1,600 training compounds.**
6. **Evaluate once on the 400 test compounds.** This is your reported performance.

If the test set performance is substantially worse than the cross-validated performance, something may be wrong — possible overfitting to the validation folds, data leakage, or a distribution shift between training and test sets.

## What Can Go Wrong

- **Using the test set for model selection.** If you try 20 models and report the one that performs best on the test set, you have effectively used the test set for tuning. The reported performance is optimistic.
- **Leaking information through preprocessing.** If you scale features, impute missing values, or select features using the full dataset (including test examples), you have leaked information.
- **Too-small test set.** A test set of 50 compounds gives noisy performance estimates. Aim for at least 10–20% of the data, or use cross-validation if the dataset is small.
- **Non-representative splits.** If the test set happens to contain only easy examples (close analogs of training compounds), performance will be inflated. Chapter 16 addresses this in detail.

## Practical Decision Rules

1. **Always hold out a test set before any modeling begins.** Lock it away and do not touch it until final evaluation.
2. **Use cross-validation for model selection and hyperparameter tuning** on the training data.
3. **Scale features, impute missing values, and select features using only the training fold** in each cross-validation iteration.
4. **Report test set performance as the primary result.** Cross-validated performance is useful for model development but is not a substitute for independent testing.
5. **Repeat evaluations with different random seeds** and report mean ± standard deviation to account for randomness.

## Mini-Summary

Disciplined data splitting — into training, validation, and test sets — is the foundation of honest model evaluation. The training set is for learning, the validation set is for tuning, and the test set is for final, independent evaluation. Data leakage, where test information contaminates training, is the most common source of inflated performance claims. Cross-validation provides robust estimates when data is limited. Treat your test set like a sealed envelope — open it only once, at the end.

## Exercises and Thought Questions

1. You have 500 compounds. How would you split them into training, validation, and test sets? What trade-offs are involved?

2. A colleague reports cross-validated R² = 0.85 but test set R² = 0.60. What might explain this gap?

3. You scale all features to zero mean and unit variance using the full dataset before splitting into train and test. Why is this a form of data leakage? How would you fix it?

4. Why is it important to repeat cross-validation with different random seeds? What does the standard deviation across repeats tell you?

5. A paper reports "leave-one-out cross-validation" performance. What are the advantages and disadvantages of this approach compared to 5-fold cross-validation?
