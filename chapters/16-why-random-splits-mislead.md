# Chapter 16 — Why Random Splits Can Mislead in Chemistry

---

## Chemical Motivation

In most ML textbooks, data is split randomly into training and test sets. For generic datasets — images of cats and dogs, movie reviews, housing prices — this is usually fine. But chemistry is different. Chemical datasets have structure: compounds come in series, analogs cluster together, and close structural relatives often appear in both training and test sets after a random split.

When close analogs of a test compound appear in the training set, the model does not need to learn generalizable chemistry — it only needs to memorize the training examples and interpolate. This makes the model look much smarter than it actually is. Understanding why random splits mislead in chemistry is one of the most important lessons in this book.

## Core Concept

### Random Split Inflation

In a random split, each compound has an equal probability of landing in the training or test set. For a dataset of drug-like compounds, this means that close analogs — compounds differing by a single methyl group, a fluorine substitution, or a ring modification — will often be split across training and test sets.

A model that sees compound A (IC50 = 10 nM) in training and is asked to predict compound B (IC50 = 15 nM, differing from A by one methyl group) in the test set has an easy job. It can predict "about 10 nM" based on similarity alone, without learning any transferable SAR.

This inflates apparent performance. The model appears to predict well, but it is really just interpolating between close analogs. When deployed on genuinely new chemistry — a new scaffold, a new chemical series — performance drops dramatically.

### Scaffold Split

A scaffold split groups compounds by their core scaffold (typically the Murcko scaffold — the ring system and linkers with side chains removed) and ensures that all compounds sharing a scaffold are in the same split. The test set contains only scaffolds not seen during training.

This is a much harder test. The model must generalize from one scaffold to another — it must learn transferable SAR, not just memorize analog series.

Scaffold splits typically produce lower performance numbers than random splits. This is not because the model is worse — it is because the evaluation is more honest.

### Time Split

A time split uses the temporal order of data collection. Compounds tested before a cutoff date go into the training set; compounds tested after go into the test set. This mimics real-world deployment: the model is trained on historical data and used to predict future compounds.

Time splits are particularly important for drug discovery, where the goal is to predict the activity of compounds that have not yet been synthesized.

### Series Split

A series split groups compounds by chemical series (e.g., all pyrazole-based analogs in one group, all indole-based analogs in another) and ensures that entire series are held out. This tests whether the model can generalize across series, not just within them.

### Project Split

A project split holds out entire projects or campaigns. If your data comes from multiple drug discovery projects, you can train on some projects and test on others. This is the most stringent test of generalization.

### Assay Leakage

A subtle form of leakage occurs when the same compound is tested in multiple assays, and information from one assay leaks into the prediction of another. For example, if a compound's hERG activity is correlated with its CYP inhibition, and both are in the feature set, the model may use one to predict the other rather than learning from molecular structure.

## Chemist-Friendly Intuition

Think of random splits as testing a student by asking questions from the same problem set they studied. The student may score well, but you do not know whether they understand the material or just memorized the answers.

A scaffold split is like testing the student on a new type of problem — same principles, different context. This is a much better test of understanding.

In medicinal chemistry terms: a random split tests whether the model can interpolate within known SAR series. A scaffold split tests whether the model can extrapolate to new chemical matter. The latter is almost always what you actually need.

## Concrete Chemical Example

You have 3,000 compounds tested against a kinase target, spanning 50 different scaffolds. You compare two evaluation strategies:

**Random split:** 80% train, 20% test, randomly assigned. The test set contains analogs of training compounds from the same scaffolds. The model achieves R² = 0.82.

**Scaffold split:** 40 scaffolds in training, 10 scaffolds in test. The test set contains only scaffolds not seen during training. The model achieves R² = 0.55.

The difference — 0.82 vs. 0.55 — is not because the model changed. It is because the evaluation changed. The random split was measuring interpolation; the scaffold split was measuring generalization. The scaffold split gives a more honest estimate of how the model will perform on genuinely new chemistry.

## What Can Go Wrong

- **Reporting only random split performance.** This is the most common mistake. It makes models look better than they are and misleads readers about real-world utility.
- **Scaffold definition ambiguity.** Different scaffold decomposition algorithms (Murcko, generic scaffolds, CSK) give different splits. The choice of scaffold definition affects the results.
- **Unbalanced splits.** A scaffold split may put most compounds in the training set and only a few in the test set (or vice versa) if scaffold sizes are uneven. Stratification or careful scaffold assignment can help.
- **Ignoring temporal structure.** If your data has a temporal component (compounds tested over time), a random split can mix past and future, creating an unrealistically easy evaluation.

## Practical Decision Rules

1. **Always report scaffold split performance** for molecular property prediction tasks. Random split performance can be reported as a supplement but should not be the primary metric.
2. **Use time splits when temporal order is available** and when the goal is prospective prediction.
3. **Compare random and scaffold split performance.** A large gap between them indicates that the model is relying on analog interpolation rather than learning transferable patterns.
4. **Document your split strategy.** Report the scaffold decomposition method, the number of scaffolds in each split, and the number of compounds per split.
5. **Be skeptical of papers that report only random split results** for chemistry tasks. Ask: "Would this performance hold on a scaffold split?"

## Mini-Summary

Random splits in chemistry datasets allow close analogs to appear in both training and test sets, inflating apparent model performance. Scaffold splits, time splits, and series splits provide more honest evaluations by testing whether the model generalizes to genuinely new chemistry. The split strategy is often as important as the model choice. Always use chemistry-aware splits for chemistry problems.

## Exercises and Thought Questions

1. Take a dataset of 1,000 compounds and compare model performance under random split vs. scaffold split. How large is the gap? What does it tell you?

2. You have a dataset spanning 2018–2023. Design a time-split evaluation strategy. What are the advantages over a random split?

3. A paper reports R² = 0.90 on a molecular property prediction task using random splits. You reproduce the experiment with scaffold splits and get R² = 0.50. Which number is more relevant for real-world use?

4. Two scaffolds in your dataset have 500 compounds each, while 48 scaffolds have fewer than 20 compounds each. How does this affect scaffold split evaluation?

5. Can a model that performs well on scaffold splits still fail in practice? What additional challenges might arise in real deployment?
