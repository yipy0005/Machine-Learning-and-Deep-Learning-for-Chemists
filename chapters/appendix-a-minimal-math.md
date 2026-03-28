# Appendix A — Minimal Math for Chemists

---

This appendix provides the minimal mathematical background needed to follow the concepts in this book. It is not a math course — it is a translation guide, explaining mathematical ideas in terms chemists already understand.

## Vectors

A vector is an ordered list of numbers. In chemistry ML, a vector typically represents a molecule:
- A descriptor vector: [MW=350, cLogP=2.5, TPSA=80, HBD=2, HBA=5]
- A fingerprint: [1, 0, 1, 1, 0, 0, 1, 0, ...] (2048 bits)

Think of a vector as a row in a spreadsheet — each column is a feature, and the values describe one molecule.

The length (or norm) of a vector measures its magnitude. The distance between two vectors measures how different two molecules are in feature space.

## Matrices

A matrix is a table of numbers — rows and columns. In chemistry ML, a matrix typically represents a dataset:
- Rows = molecules
- Columns = features
- Each cell = the value of one feature for one molecule

Matrix operations (multiplication, inversion, decomposition) are the computational backbone of many ML algorithms, but you rarely need to perform them by hand.

## Functions

A function maps inputs to outputs. In ML, the model is a function:
- Input: molecular representation (vector)
- Output: predicted property (number or category)

Training a model means finding the function that best maps inputs to outputs for the training data.

## Gradients

A gradient tells you how the output changes when you change the input. In ML, gradients tell the model how to adjust its parameters to reduce prediction error.

Think of a gradient as the slope of a hill. If you are trying to reach the bottom of a valley (minimize error), the gradient tells you which direction is downhill and how steep the slope is.

## Optimization

Optimization is the process of finding the best parameters for a model — the parameters that minimize prediction error. Most ML optimization uses gradient descent: take a step in the direction that reduces the error, repeat until you reach a minimum.

This is analogous to optimizing a reaction. You adjust conditions (parameters) to maximize yield (minimize error), guided by how the yield changes with each adjustment (the gradient).

## Probability Basics

Probability quantifies uncertainty. In ML:
- A classification model outputs the probability that a compound belongs to each class (e.g., 70% active, 30% inactive).
- Probability distributions describe the range of possible values for a measurement or prediction.
- Bayes' theorem updates probabilities based on new evidence — the mathematical foundation of learning from data.

A probability of 0.7 means: "If I saw 100 compounds like this, about 70 would be active." It does not mean this specific compound is 70% active — it is either active or not. Probability quantifies our uncertainty about which.
