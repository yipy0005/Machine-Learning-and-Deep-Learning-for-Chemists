# Chapter 13 — Decision Trees, Random Forests, and Gradient Boosting

---

## Chemical Motivation

When a chemist evaluates a compound, they often apply a series of conditional rules: "If the compound is lipophilic AND has a basic nitrogen, it might accumulate in lysosomes." "If the molecular weight is above 500 AND it has poor solubility, oral bioavailability is unlikely." This sequential, conditional reasoning is exactly what decision trees do — and their ensemble extensions (random forests and gradient boosting) are among the most powerful and practical models for chemistry ML.

## Core Concept

### Decision Trees

A decision tree is a model that makes predictions by asking a sequence of yes/no questions about the input features. Each question splits the data into two groups, and the process repeats until the groups are pure enough (for classification) or the predictions are accurate enough (for regression).

Think of it as a flowchart. At each node, the tree asks a question like "Is cLogP > 3?" If yes, go left; if no, go right. At the bottom of the tree (the leaves), you find the prediction.

The tree learns which questions to ask and where to set the thresholds by examining the training data. It finds the splits that best separate the data — the questions that are most informative about the target property.

A single decision tree is interpretable — you can trace the path from root to leaf and understand exactly why the model made its prediction. However, single trees tend to overfit: they memorize the training data rather than learning generalizable patterns. They are like a chemist who memorizes specific compounds rather than learning transferable SAR principles.

### Random Forests

A random forest addresses overfitting by building many trees, each trained on a slightly different version of the data (using random subsampling) and considering a random subset of features at each split. The final prediction is the average (regression) or majority vote (classification) of all trees.

The chemical analogy is consensus. Instead of relying on one chemist's opinion, you poll a committee of chemists, each with slightly different experience and perspective. The consensus prediction is more robust than any individual opinion.

Random forests are:
- Resistant to overfitting (the ensemble averages out individual tree errors)
- Able to capture nonlinear relationships and feature interactions
- Relatively insensitive to feature scaling (unlike linear models)
- Fast to train and predict
- Able to provide feature importance estimates

### Gradient Boosting

Gradient boosting builds trees sequentially rather than independently. Each new tree focuses on the errors made by the previous trees, gradually correcting mistakes. The final prediction is the sum of all trees' contributions.

Think of it as iterative refinement. The first tree captures the broad pattern. The second tree focuses on what the first tree got wrong. The third tree corrects the remaining errors, and so on. Each tree is small and weak on its own, but the ensemble is powerful.

Gradient boosting methods (XGBoost, LightGBM, CatBoost) are often the top-performing models on tabular data — data organized as rows (compounds) and columns (features). For descriptor and fingerprint data, they are frequently the best choice.

### Nonlinear Feature Interactions

The key advantage of tree-based models over linear models is their ability to capture interactions. A linear model assumes that the effect of cLogP on solubility is the same regardless of molecular weight. A tree can learn that cLogP matters more for large molecules than for small ones — an interaction effect.

In chemical terms, trees can learn conditional SAR: "For compounds with a basic nitrogen, lipophilicity drives activity; for compounds without a basic nitrogen, polarity drives activity." This conditional reasoning is natural for chemists and natural for trees.

## Chemist-Friendly Intuition

A decision tree is a formalized version of the decision rules chemists apply every day. Lipinski's Rule of Five is essentially a shallow decision tree with four splits. A medicinal chemist's mental model for predicting hERG liability ("if cLogP > 3 and the compound has a basic amine, flag for hERG testing") is a two-node decision tree.

Random forests and gradient boosting extend this by combining many such rule sets, each capturing different aspects of the SAR. The ensemble captures complexity that no single rule set can express, while remaining grounded in the same conditional logic that chemists use intuitively.

## Concrete Chemical Example

You have 5,000 compounds with measured CYP3A4 inhibition (IC50 values) and 200 molecular descriptors. You train a random forest:

1. The forest builds 500 trees, each trained on a bootstrap sample of the data.
2. Each tree considers a random subset of ~14 descriptors at each split (the square root of 200).
3. The trees learn rules like: "If aromatic ring count > 2 AND molecular weight > 400 AND TPSA < 60, predict strong inhibition."
4. The final prediction for each compound is the average of all 500 trees' predictions.

Feature importance analysis reveals that cLogP, aromatic ring count, and the number of nitrogen atoms are the most important features — consistent with known SAR for CYP3A4 inhibition.

You compare to a linear model (R² = 0.55) and find the random forest achieves R² = 0.72. The improvement comes from the forest's ability to capture nonlinear interactions between features that the linear model misses.

## What Can Go Wrong

- **Feature importance can be misleading.** Random forest feature importance measures how much each feature contributes to the splits, but correlated features share importance. If cLogP and number of aromatic carbons are correlated, the importance is split between them, making both appear less important than they are.

- **Overfitting with deep trees.** A single deep tree will memorize the training data. Always use ensembles (random forests or boosting) rather than single trees for prediction.

- **Poor extrapolation.** Tree-based models predict by averaging training labels in leaf nodes. They cannot predict values outside the range of the training data. If your training solubilities range from -1 to -6 log units, the model cannot predict -8.

- **Sensitivity to hyperparameters in boosting.** Gradient boosting has more hyperparameters than random forests (learning rate, number of trees, tree depth, regularization). Poor hyperparameter choices can lead to overfitting or underfitting. Use cross-validation to tune.

- **Ignoring the representation.** Tree-based models work well on descriptors and fingerprints but cannot directly process graphs or SMILES strings. The representation must be a fixed-length feature vector.

## Practical Decision Rules

1. **Random forests are the best default model** for descriptor and fingerprint data. They are robust, fast, and require minimal tuning.

2. **Gradient boosting (XGBoost, LightGBM) often outperforms random forests** but requires more careful hyperparameter tuning. Use it when you need the best possible performance on tabular data.

3. **Use feature importance as a guide, not as ground truth.** Cross-reference with chemical knowledge and consider using permutation importance or SHAP values for more reliable estimates.

4. **Set a maximum tree depth** (typically 5–15) to prevent overfitting. Deeper trees capture more complex interactions but risk memorizing noise.

5. **Tree-based models are your strongest tool for tabular chemical data.** Before reaching for a neural network, make sure a well-tuned random forest or gradient boosting model is not already sufficient.

## Mini-Summary

Decision trees learn conditional rules from data, capturing nonlinear relationships and feature interactions that linear models miss. Random forests and gradient boosting combine many trees into powerful ensembles that are robust, accurate, and practical. For descriptor and fingerprint data, tree-based models are often the best first-line choice and a strong baseline against which more complex models should be compared.

## Exercises and Thought Questions

1. Build a single decision tree and a random forest (100 trees) on the same dataset. Compare their training and test performance. What do you observe about overfitting?

2. A random forest reports that "number of rotatable bonds" is the most important feature for predicting oral bioavailability. How would you verify whether this is a genuine chemical signal or an artifact of correlation with other features?

3. Your gradient boosting model achieves excellent cross-validated performance but poor performance on an external test set. What might explain this? How would you diagnose the problem?

4. Compare a random forest on Morgan fingerprints (2048 bits) to a random forest on 200 RDKit descriptors for the same prediction task. Which performs better? What does this tell you about the representation?

5. A tree-based model predicts that a compound with logP = 8 will have the same solubility as a compound with logP = 6 (both fall in the same leaf node). Why does this happen, and is it a problem?
