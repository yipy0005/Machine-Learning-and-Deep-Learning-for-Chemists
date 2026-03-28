# Chapter 3 — Turning Chemical Questions into Machine Learning Tasks

---

## Chemical Motivation

A chemist walks into a meeting and says, "We need a model." But a model for what, exactly? Predicting a number? Sorting compounds into categories? Ranking candidates? Generating new structures? Grouping analogs?

One of the most common sources of confusion in chemistry ML is not the algorithm — it is the failure to define the task clearly. Before you choose a representation or a model, you must translate your chemical question into a well-defined ML task type. This translation step is like choosing the right type of experiment: you would not run a titration when you need a chromatographic separation, even though both involve solutions.

## Core Concept

Machine learning tasks fall into a small number of well-defined categories. Each category corresponds to a different type of question and requires different models, different evaluation metrics, and different data formats.

The major task types are:

| Task Type | Question Form | Output |
|---|---|---|
| Regression | "How much?" | A continuous number |
| Classification | "Which category?" | A discrete label |
| Ranking | "Which is better?" | An ordered list |
| Clustering | "What groups exist?" | Group assignments |
| Recommendation | "What should I try next?" | Suggested items |
| Generation | "What could exist?" | New structures |
| Anomaly detection | "What is unusual?" | Outlier flags |

Each of these maps naturally onto chemical questions that chemists ask every day.

## Chemist-Friendly Intuition

### Regression: Predicting a Quantity

When you ask "What is the melting point of this compound?" or "What yield will this reaction give?" you are asking a regression question. The answer is a number on a continuous scale.

In chemical terms, regression is like building a quantitative calibration curve. You have known standards (training data with measured values), and you want to predict the response for an unknown sample.

**Chemical examples:**
- Predict aqueous solubility (log S) from molecular structure
- Predict boiling point from composition
- Predict reaction yield from conditions
- Predict binding free energy from protein–ligand complex features

The key requirement is that your labels are continuous, quantitative measurements.

### Classification: Assigning Categories

When you ask "Is this compound toxic or non-toxic?" or "Will this reaction succeed or fail?" you are asking a classification question. The answer is a category.

Classification is like a qualitative test — a litmus test that says acid or base, not pH 3.7. You lose quantitative resolution but gain a clear decision boundary.

**Chemical examples:**
- Active vs. inactive in a biological assay (binary classification)
- Ames-positive vs. Ames-negative for mutagenicity
- Reaction type classification (substitution, elimination, addition, rearrangement)
- Crystal system prediction (cubic, tetragonal, orthorhombic, etc.) — multiclass classification

A critical decision in classification is where to set the threshold. If you define "active" as IC50 < 1 μM, you get a different dataset than if you define it as IC50 < 10 μM. This threshold is a chemical decision, not a modeling decision, and it should be made before training begins.

### Ranking: Ordering by Priority

When you ask "Which of these 500 compounds should I test first?" you are asking a ranking question. You do not need exact predicted values — you need the correct order.

Ranking is like triage in a screening campaign. You want the most promising candidates at the top of the list, even if you cannot predict their exact activity.

**Chemical examples:**
- Prioritize compounds for synthesis based on predicted potency
- Rank reaction conditions by expected yield
- Order materials candidates by predicted band gap

Ranking is subtly different from regression. A model that gets the absolute numbers wrong but the order right may be more useful for decision-making than a model that gets the numbers closer but scrambles the order.

### Clustering: Discovering Groups

When you ask "Are there natural groupings in this compound set?" you are asking a clustering question. There is no target label — you are looking for structure in the data itself.

Clustering is like sorting a compound library by structural similarity without predefined categories. You let the data reveal its own organization.

**Chemical examples:**
- Group analogs into chemical series
- Identify clusters of similar spectra in a screening plate
- Segment a materials dataset into families with similar properties
- Discover reaction classes from a database of transformations

Clustering is an unsupervised task — there are no "right answers" to train on. This makes evaluation harder. You must judge clusters by whether they are chemically meaningful, not by a simple accuracy number.

### Recommendation: Suggesting What to Try Next

When you ask "Given what we know so far, what experiment should we run next?" you are asking a recommendation question. The model suggests items (compounds, conditions, experiments) based on patterns in previous data.

This is closely related to active learning (Chapter 30) and is one of the most practically valuable applications of ML in chemistry.

**Chemical examples:**
- Suggest the next compound to synthesize in a lead optimization campaign
- Recommend reaction conditions for a new substrate based on similar substrates
- Propose materials compositions to test based on previous screening results

### Generation: Creating New Entities

When you ask "What molecules could have this property?" you are asking a generation question. The model proposes new structures that do not exist in the training data.

Generation is like combinatorial chemistry guided by a learned model rather than by exhaustive enumeration. The model has learned what "reasonable" molecules look like and can propose new ones.

**Chemical examples:**
- Generate novel drug candidates with predicted activity against a target
- Propose new polymer structures with desired thermal properties
- Design molecules that fill a gap in chemical space

Generation is exciting but treacherous. It is easy to generate molecules that look plausible on paper but are unsynthesizable, unstable, or uninteresting. Validation requires experimental follow-up, not just computational metrics.

### Anomaly Detection: Finding the Unusual

When you ask "Which of these measurements looks suspicious?" or "Is this compound behaving unexpectedly?" you are asking an anomaly detection question. The model learns what "normal" looks like and flags deviations.

**Chemical examples:**
- Detect outlier measurements in a high-throughput screen
- Identify unusual spectra that may indicate contamination or novel compounds
- Flag compounds whose predicted and measured properties disagree dramatically

## Concrete Chemical Example

A medicinal chemistry team has the following data: 5,000 compounds tested in a biochemical assay against kinase X, with IC50 values ranging from 0.1 nM to > 100 μM. They want to use ML to support their project. But what task should they define?

**Option 1: Regression.** Predict the exact IC50 value. This is appropriate if the team needs quantitative potency estimates — for example, to predict whether a compound will meet a potency threshold.

**Option 2: Classification.** Define "active" as IC50 < 100 nM and predict active vs. inactive. This is appropriate if the team only cares about identifying potent compounds, not distinguishing between 10 nM and 50 nM.

**Option 3: Ranking.** Rank untested compounds by predicted potency. This is appropriate if the team wants to prioritize a large virtual library for experimental testing.

**Option 4: Clustering.** Group the 5,000 compounds by structural similarity and activity pattern to identify distinct chemical series. This is appropriate for understanding the SAR landscape.

Each option leads to a different model, different evaluation metrics, and different practical utility. The "right" choice depends on the scientific question and the decision the model will support.

## What Can Go Wrong

- **Mismatched task and question.** Training a regression model when you actually need a ranking, or a classifier when you need a quantitative prediction. The model may perform well by its own metrics but fail to answer the question you actually care about.
- **Poorly defined thresholds.** In classification, the choice of threshold (e.g., what counts as "active") is a scientific decision with real consequences. A threshold that is too lenient includes noise; one that is too strict discards useful data.
- **Confusing generation with discovery.** A generative model that proposes novel molecules has not discovered anything. Discovery requires experimental validation. Treating generated molecules as discoveries is a category error.
- **Forcing a supervised task on unsupervised data.** If you do not have reliable labels, do not pretend you do. Clustering or representation learning may be more appropriate than classification or regression.

## Practical Decision Rules

1. **Start by writing down the question in plain chemical language.** "I want to predict..." or "I want to find..." or "I want to group..." The verb tells you the task type.

2. **Match the task type to your data.** Regression requires continuous labels. Classification requires categorical labels. Clustering requires no labels. Generation requires a training corpus of valid examples.

3. **Consider the downstream decision.** If the model's output will be used to rank compounds for testing, evaluate it as a ranking task, even if you train it as a regression model.

4. **Define thresholds before training, not after.** If you are converting a continuous measurement into a binary label, choose the threshold based on chemical or biological reasoning, not based on what makes the model look best.

5. **Be honest about what you do not have.** If your labels are noisy, sparse, or censored, acknowledge this and choose a task formulation that accommodates it.

## Mini-Summary

Every chemistry ML project begins with a translation step: converting a chemical question into a well-defined ML task. The major task types — regression, classification, ranking, clustering, recommendation, generation, and anomaly detection — each correspond to different chemical questions and require different approaches. Getting this translation right is one of the most important and most overlooked steps in the entire workflow. A model that solves the wrong task perfectly is useless.

## Exercises and Thought Questions

1. For each of the following chemical questions, identify the most appropriate ML task type:
   - "What is the predicted logP of this compound?"
   - "Is this compound likely to be a P-gp substrate?"
   - "Which 50 compounds from this 10,000-compound library should we test first?"
   - "Are there distinct families of catalysts in this dataset?"
   - "What new molecules might inhibit this enzyme?"

2. You have a dataset of reaction yields (0–100%). A colleague suggests converting yields into three categories: low (< 30%), medium (30–70%), and high (> 70%) and training a classifier. What are the advantages and disadvantages of this approach compared to training a regression model on the raw yields?

3. A generative model proposes 1,000 novel molecules predicted to be active against your target. How would you decide which ones (if any) to actually synthesize?

4. You want to cluster a library of 50,000 compounds into structurally similar groups. What would make a clustering "good"? How would you evaluate it without ground-truth labels?

5. Your team needs to choose between two models: one that predicts IC50 values accurately but ranks compounds poorly, and one that ranks compounds well but predicts IC50 values with large errors. Which would you choose for a virtual screening campaign? For a quantitative pharmacokinetic model?
