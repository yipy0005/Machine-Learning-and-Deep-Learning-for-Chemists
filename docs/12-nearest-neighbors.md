# Chapter 12 — Nearest Neighbors and Similarity-Based Prediction

---

## Chemical Motivation

The most natural reasoning a chemist uses is analogy: "This compound looks like that known active, so it might also be active." This is nearest-neighbor reasoning — predicting the behavior of an unknown from the behavior of the most similar known example. It is the computational formalization of the similarity principle that underpins medicinal chemistry, toxicology, and materials science.

## Core Concept

A nearest-neighbor model makes predictions by finding the training examples most similar to the query and using their labels as the prediction. For regression, the prediction is typically the average of the neighbors' values. For classification, it is the majority vote.

The model has no training phase in the traditional sense — it simply stores the training data and performs a lookup at prediction time. The key decisions are:

- **How to measure similarity** — Tanimoto similarity on fingerprints, Euclidean distance on descriptors, or other metrics.
- **How many neighbors to use** — one (the single most similar compound) or several (k nearest neighbors, where k is typically 3–10).
- **How to weight neighbors** — equally, or weighted by similarity (closer neighbors count more).

### Local Similarity and the Similarity Principle

The similarity principle states that structurally similar compounds tend to have similar properties. This is not a law of nature — it is an empirical observation that holds often enough to be useful but fails often enough to require caution.

Activity cliffs — pairs of very similar molecules with very different activities — are the most dramatic violations of the similarity principle. A single methyl group addition or a fluorine-to-chlorine substitution can change activity by orders of magnitude. Nearest-neighbor models are particularly vulnerable to activity cliffs because they rely entirely on local similarity.

### Applicability Domain

A nearest-neighbor model naturally defines an applicability domain — the region of chemical space where predictions are reliable. If the nearest neighbor is very similar (high Tanimoto), the prediction is likely reliable. If the nearest neighbor is distant (low Tanimoto), the model is extrapolating, and the prediction should be treated with caution.

This is one of the most valuable properties of nearest-neighbor models. They come with a built-in confidence measure: the similarity to the nearest training example. No other model family provides this as naturally.

### Interpolation vs. Extrapolation

Nearest-neighbor models interpolate — they predict within the convex hull of the training data. They do not extrapolate — they cannot reliably predict for compounds that are structurally unlike anything in the training set.

This is analogous to a calibration curve. Within the calibrated range, predictions are reliable. Outside the range, you are extrapolating, and the uncertainty grows rapidly. A nearest-neighbor model makes this boundary explicit through the similarity of the nearest neighbor.

## Chemist-Friendly Intuition

A nearest-neighbor model is the computational version of asking an experienced chemist: "Have you seen anything like this before?" If the answer is "Yes, and it was active," the prediction is "probably active." If the answer is "I've never seen anything like this," the honest prediction is "I don't know."

This is how medicinal chemists actually reason during lead optimization. They look at the SAR table, find the closest analog to the proposed compound, and estimate its activity based on the analog's activity plus any expected effect of the structural change.

The nearest-neighbor model automates this process and scales it to thousands of compounds, but the underlying logic is identical.

## Concrete Chemical Example

You are screening a library of 10,000 compounds against a kinase target. You have activity data for 500 compounds from a previous campaign. For each untested compound:

1. Compute its Morgan fingerprint.
2. Calculate Tanimoto similarity to all 500 tested compounds.
3. Find the 5 most similar tested compounds.
4. If the majority are active, predict active; if the majority are inactive, predict inactive.
5. Report the similarity to the nearest neighbor as a confidence measure.

Compounds with a nearest neighbor at Tanimoto > 0.7 get reliable predictions. Compounds with no neighbor above Tanimoto 0.4 are outside the applicability domain — the model cannot make a confident prediction.

This simple approach often performs surprisingly well for virtual screening, especially when the training set covers the relevant chemical space.

## What Can Go Wrong

- **Activity cliffs.** The nearest neighbor may be very similar but have very different activity. The model has no way to anticipate this.
- **Sparse chemical space.** If the training set is small or covers a narrow region of chemical space, most query compounds will have distant neighbors, and predictions will be unreliable.
- **Curse of dimensionality.** In high-dimensional descriptor spaces, all compounds become approximately equidistant, and the concept of "nearest" loses meaning. Fingerprint-based similarity is more robust to this than Euclidean distance on descriptors.
- **Computational cost at scale.** Finding nearest neighbors in a large database requires computing similarity to every training example, which can be slow. Approximate nearest-neighbor algorithms can help.

## Practical Decision Rules

1. **Use nearest-neighbor models as a baseline** for any chemistry prediction task. They test the similarity principle directly.
2. **Use Tanimoto similarity on Morgan fingerprints** as the default similarity measure for drug-like molecules.
3. **Report the similarity to the nearest neighbor** as a confidence measure alongside every prediction.
4. **Define an applicability domain threshold** — predictions for compounds with no similar training example should be flagged as unreliable.
5. **Combine nearest-neighbor predictions with other models** in ensemble approaches to get the benefits of both local similarity and global pattern learning.

## Mini-Summary

Nearest-neighbor models predict by finding the most similar training examples and using their labels. They formalize the similarity principle that chemists use intuitively and provide a natural applicability domain through the similarity to the nearest neighbor. Their main limitations are vulnerability to activity cliffs and inability to extrapolate beyond the training data. They are an essential baseline and a valuable component of any chemistry ML toolkit.

## Exercises and Thought Questions

1. For a dataset of 1,000 compounds with measured solubility, compare a 1-nearest-neighbor model to a 5-nearest-neighbor model. Which performs better? Why?

2. You find that your nearest-neighbor model performs well for compounds with Tanimoto > 0.6 to the nearest training example but poorly below 0.6. How would you use this information in practice?

3. Two compounds have Tanimoto similarity of 0.92 but differ in activity by 100-fold. What does this tell you about the similarity principle? How would you handle this in your model?

4. Compare nearest-neighbor prediction using Morgan fingerprints vs. MACCS keys for the same dataset. Do they give the same results? Why or why not?

5. A nearest-neighbor model and a random forest achieve similar performance on your dataset. Which would you deploy in practice, and why?
