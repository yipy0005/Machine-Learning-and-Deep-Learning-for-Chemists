# The Grand Decision Workflow

A complete decision guide for chemists who need answers now. Each decision box links to the relevant chapter for deeper reading.

---

## The Flowchart

```
┌─────────────────────────────────────┐
│  You have a chemical problem and    │
│  think ML might help.               │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│  STEP 1: Define the Question        │
│  What exactly are you predicting?   │
│  What decision will it support?     │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│  STEP 2: Audit Your Data            │
│  Is the data clean, consistent,     │
│  and large enough?                  │
└──────────────┬──────────────────────┘
               │
          ┌────┴────┐
          │  NO     │  YES
          ▼         ▼
     ┌─────────┐  ┌──────────────────┐
     │ Fix the │  │ STEP 3: Choose   │
     │ data    │  │ the task type    │
     │ first.  │  └────────┬─────────┘
     └─────────┘           │
                           ▼
               ┌───────────────────────┐
               │ STEP 4: Choose        │
               │ representation        │
               └───────────┬───────────┘
                           │
                           ▼
               ┌───────────────────────┐
               │ STEP 5: Establish     │
               │ baselines             │
               └───────────┬───────────┘
                           │
                           ▼
               ┌───────────────────────┐
               │ STEP 6: Build, eval,  │
               │ iterate               │
               └───────────┬───────────┘
                           │
                           ▼
               ┌───────────────────────┐
               │ STEP 7: Quantify      │
               │ uncertainty, deploy   │
               └───────────────────────┘
```

---

## Step 1 — Define the Question

*Before touching any code, write down the answer to these three questions:*

| Question | Your Answer |
|---|---|
| What am I predicting? | e.g., solubility, yield, activity, toxicity |
| What decision will the prediction support? | e.g., prioritize compounds, flag liabilities, guide synthesis |
| What task type is this? | See the table below |

### Task Type Decision Table

| If you are asking... | Task type | Example |
|---|---|---|
| "How much?" (a number) | **Regression** | Predict logS, yield, pIC50 |
| "Which category?" (a label) | **Classification** | Active vs. inactive, toxic vs. non-toxic |
| "What order?" (a ranking) | **Ranking** | Prioritize 500 compounds for screening |
| "What groups exist?" (no labels) | **Clustering** | Group analogs into chemical series |
| "What should I try next?" | **Recommendation / Active learning** | Choose next experiment |
| "What could exist?" (new structures) | **Generation** | Propose novel molecules |
| "What looks unusual?" | **Anomaly detection** | Flag suspicious measurements |

📖 *Deep dive: [Chapter 1](01-what-machine-learning-is.md), [Chapter 3](03-turning-chemical-questions-into-ml-tasks.md)*

---

## Step 2 — Audit Your Data

*Do not skip this. Data quality sets the ceiling for any model.*

### Data Audit Checklist

- [ ] **How many examples?** Record the count. This drives every downstream decision.
- [ ] **Where did the data come from?** Single lab? Multiple sources? Public database?
- [ ] **Are there duplicates?** Deduplicate by canonical SMILES. Aggregate or choose among conflicting values.
- [ ] **What is the noise floor?** If replicates exist, compute the standard deviation. This is the best any model can do.
- [ ] **Are there missing values?** How many? Random or systematic?
- [ ] **Are there censored values?** (e.g., "> 100 μM") Do not treat these as exact numbers.
- [ ] **Are labels from a consistent protocol?** Mixing assay formats introduces noise no model can fix.
- [ ] **Are there batch effects or confounds?** Check if the target correlates with non-chemical variables (date, plate, operator).

### Data Size Reality Check

| Data size | What is realistic |
|---|---|
| < 100 | Very limited. Simple models only. Consider whether ML is appropriate. |
| 100–500 | Classical ML with descriptors. Careful cross-validation essential. |
| 500–5,000 | Classical ML works well. Deep learning becomes viable at the upper end. |
| 5,000–50,000 | Deep learning is justified. GNNs and pretrained models shine here. |
| > 50,000 | Full deep learning. Large architectures and pretraining pay off. |

📖 *Deep dive: [Chapter 2](02-chemical-data.md)*

---

## Step 3 — Choose the Representation

*The model only sees what you show it. Choose based on what chemical information matters.*

```
What information drives the property?
│
├── Bulk physicochemical properties (MW, logP, PSA)
│   └── → Molecular descriptors
│
├── Substructural features (functional groups, ring systems)
│   └── → Fingerprints (Morgan radius 2, 2048 bits)
│
├── Full molecular topology (connectivity, atom environments)
│   └── → Molecular graphs
│
├── 3D shape, stereochemistry, binding geometry
│   └── → 3D coordinates
│
├── Spectral signatures (NMR, IR, MS)
│   └── → Raw spectra (for CNNs)
│
├── Multiple types of information (structure + conditions)
│   └── → Multimodal (combine encoders)
│
└── Not sure?
    └── → Start with descriptors + fingerprints. Add complexity if needed.
```

### Quick Representation Guide

| Representation | Best for | Needs | Pairs with |
|---|---|---|---|
| Descriptors | Small data, interpretability | Nothing special | Linear models, trees, dense nets |
| Fingerprints | Similarity, substructure | RDKit | Trees, SVMs, dense nets |
| SMILES | Generation, pretraining | Tokenizer | Sequence models, transformers |
| Graphs | Topology-dependent properties | Graph library | GNNs |
| 3D coordinates | Shape-dependent properties | Conformer generation | 3D GNNs |
| Combined | Complex problems | Multiple encoders | Multimodal architectures |

📖 *Deep dive: [Chapters 4–9](04-why-representation-comes-before-model-choice.md)*

---

## Step 4 — Establish Baselines

*Never skip this. Baselines are your control experiments.*

Run these four models in order. Each takes minutes, not hours:

| # | Baseline | What it tests | How to run |
|---|---|---|---|
| 1 | **Dummy** (predict mean / majority class) | Is there any signal at all? | `DummyRegressor()` / `DummyClassifier()` |
| 2 | **1-Nearest neighbor** (Tanimoto on Morgan FP) | Does simple similarity work? | `KNeighborsRegressor(n_neighbors=1)` |
| 3 | **Ridge regression** (on descriptors) | Is the relationship linear? | `Ridge(alpha=1.0)` |
| 4 | **Random forest** (on descriptors or FP) | Do nonlinear interactions help? | `RandomForestRegressor(n_estimators=500)` |

**Record all four results.** Every subsequent model must beat baseline #4 to justify its complexity.

📖 *Deep dive: [Chapter 10](10-baselines-first.md)*

---

## Step 5 — Choose the Evaluation Strategy

*How you evaluate is as important as what you build.*

### Split Strategy Decision

```
Do you have temporal data (compounds tested over time)?
├── YES → Use time split (train on past, test on future)
└── NO
    └── Are you predicting for new scaffolds?
        ├── YES → Use scaffold split (Murcko)
        └── NO (close analogs only)
            └── Random split is acceptable, but report scaffold split too
```

### Metric Decision

| Your task | Primary metric | Secondary metric |
|---|---|---|
| Regression (quantitative) | MAE or RMSE | R² |
| Regression (ranking matters) | Spearman ρ | MAE |
| Classification (balanced) | ROC-AUC | F1 |
| Classification (imbalanced) | **PR-AUC** | F1, enrichment |
| Virtual screening | **Top-k enrichment** | PR-AUC |

⚠️ **Never use accuracy for imbalanced data.**

📖 *Deep dive: [Chapters 15–18](15-train-validation-test.md)*

---

## Step 6 — Model Selection

*Start simple. Add complexity only when justified by held-out performance.*

### The Decision Tree for Model Choice

```
How much data do you have?
│
├── < 500 compounds
│   ├── Descriptors available? → Random forest or gradient boosting
│   └── Need interpretability? → Ridge regression
│
├── 500–5,000 compounds
│   ├── Tabular features (descriptors/FP)?
│   │   └── → Gradient boosting (XGBoost/LightGBM) ← TRY THIS FIRST
│   ├── Want to try deep learning?
│   │   └── → Dense neural network on descriptors/FP
│   └── Molecular graphs available?
│       └── → GNN (compare to gradient boosting baseline)
│
├── 5,000–50,000 compounds
│   ├── Tabular features? → Gradient boosting (still competitive!)
│   ├── Graphs? → GNN
│   ├── SMILES? → Pretrained transformer (fine-tune)
│   └── 3D needed? → Equivariant GNN
│
└── > 50,000 compounds
    ├── → GNN or transformer (pretrained + fine-tuned)
    ├── → 3D models if geometry matters
    └── → Multimodal if multiple data types
```

### Model Comparison Cheat Sheet

| Situation | Recommended model | Why |
|---|---|---|
| Small data, descriptors | Random forest | Robust, fast, minimal tuning |
| Medium data, best tabular performance | Gradient boosting (XGBoost) | Usually best on tabular data |
| Need interpretability | Ridge regression or single trees | Transparent coefficients/rules |
| Large data, molecular graphs | GNN (MPNN, AttentiveFP) | Learns from topology directly |
| Scarce labels, pretrained model available | Fine-tuned transformer/GNN | Transfer learning compensates |
| 3D shape matters | Equivariant GNN (SchNet, PaiNN) | Captures geometry |
| Generation task | VAE or autoregressive on SMILES | Sequence generation is mature |
| Reaction prediction | Seq2seq transformer | Treats reactions as translation |
| Multiple data types | Multimodal architecture | Combines complementary info |

📖 *Deep dive: [Chapters 11–14](11-linear-models.md), [Chapters 20–27](20-what-a-neural-network-is.md), [Chapter 36](36-choosing-the-right-model.md)*

---

## Step 7 — After Modeling

*A prediction without uncertainty is a measurement without error bars.*

### Uncertainty Checklist

- [ ] Train an ensemble (5–10 models with different seeds)
- [ ] Report ensemble mean as prediction, ensemble std as uncertainty
- [ ] Define applicability domain (e.g., Tanimoto > 0.3 to nearest training compound)
- [ ] Flag out-of-domain predictions
- [ ] Check calibration if using predicted probabilities

### Interpretation Checklist

- [ ] Run feature importance or SHAP analysis
- [ ] Cross-reference top features with chemical knowledge
- [ ] Check stability (retrain with different seeds — do explanations change?)
- [ ] **Do not treat model explanations as causal truth**

### Deployment Readiness Checklist

- [ ] Model outperforms baselines on scaffold split?
- [ ] Performance sufficient for the intended decision?
- [ ] Uncertainty estimates provided and reliable?
- [ ] Applicability domain defined?
- [ ] Limitations documented?
- [ ] Code and data versioned for reproducibility?

📖 *Deep dive: [Chapter 29](29-uncertainty-applicability-domain.md), [Chapter 19](19-interpretability.md), [Chapter 38](38-reproducibility.md)*

---

## Common Pitfalls — Quick Reference

| Pitfall | How to detect | How to fix |
|---|---|---|
| **Data leakage** | Test performance suspiciously high | Audit preprocessing; ensure no test info in training |
| **Random split inflation** | Random split R² >> scaffold split R² | Always report scaffold split |
| **Overfitting** | Train R² >> test R² | Regularize, reduce complexity, get more data |
| **Wrong metric** | High accuracy on imbalanced data | Switch to PR-AUC, F1, or enrichment |
| **No baselines** | Cannot judge if model is good | Run dummy, 1-NN, linear, and RF baselines |
| **Ignored uncertainty** | All predictions treated equally | Add ensemble uncertainty, define applicability domain |
| **Hype-driven model choice** | Using GNN/transformer on 200 compounds | Start with random forest; add complexity if justified |

📖 *Deep dive: [Chapter 37](37-common-failure-modes.md)*

---

## One-Page Summary

```
 DEFINE QUESTION → AUDIT DATA → CHOOSE REPRESENTATION → BASELINES
       │                                                     │
       │              ┌──────────────────────────────────────┘
       │              │
       │              ▼
       │         Are baselines sufficient?
       │         ├── YES → Deploy with uncertainty. Done.
       │         └── NO  → Add complexity (gradient boosting → neural net → GNN)
       │                   │
       │                   ▼
       │              Improvement on scaffold split?
       │              ├── YES → Quantify uncertainty → Deploy
       │              └── NO  → Revisit data, representation, or question
       │
       └── At every step: evaluate on scaffold split,
           compare to baselines, report uncertainty.
```

**The golden rule:** A well-designed simple model beats a poorly designed complex one. Every time.
