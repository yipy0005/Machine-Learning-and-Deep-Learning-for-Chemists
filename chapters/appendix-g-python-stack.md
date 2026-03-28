# Appendix G — Practical Python Stack for Chemistry ML

---

## Core Libraries

### Data Handling
- **pandas** — tabular data manipulation, reading/writing CSV files, data cleaning
- **numpy** — numerical arrays, mathematical operations, random number generation

### Chemistry
- **RDKit** — the essential cheminformatics toolkit
  - Molecular parsing (SMILES, SDF, InChI)
  - Descriptor computation
  - Fingerprint generation (Morgan, MACCS, topological)
  - Substructure search and molecular similarity
  - Scaffold decomposition (Murcko scaffolds)
  - 3D conformer generation
  - Molecular visualization

### Classical Machine Learning
- **scikit-learn** — the standard ML library
  - Linear models (Ridge, Lasso, Logistic Regression)
  - Tree-based models (Random Forest, Gradient Boosting)
  - SVMs
  - k-Nearest Neighbors
  - Cross-validation and hyperparameter tuning
  - Preprocessing (scaling, imputation)
  - Metrics (MAE, RMSE, R², ROC-AUC, PR-AUC, F1)

### Gradient Boosting
- **XGBoost** — fast, scalable gradient boosting
- **LightGBM** — efficient gradient boosting for large datasets
- **CatBoost** — gradient boosting with native categorical feature support

### Deep Learning
- **PyTorch** — the dominant deep learning framework for research
  - Tensor operations
  - Automatic differentiation
  - Neural network modules
  - Training loops and optimizers

### Graph Neural Networks
- **PyTorch Geometric (PyG)** — GNN library built on PyTorch
  - Graph data structures
  - Message-passing layers (GCN, GAT, MPNN)
  - Graph pooling and readout
  - Molecular graph construction utilities
- **DGL (Deep Graph Library)** — alternative GNN framework
- **DGL-LifeSci** — chemistry-specific GNN utilities

### Pretrained Models
- **HuggingFace Transformers** — pretrained language models adaptable for SMILES
- **ChemBERTa** — SMILES-based pretrained transformer
- **Uni-Mol** — 3D molecular pretrained model

### Visualization
- **matplotlib** — basic plotting
- **seaborn** — statistical visualization
- **RDKit Draw** — molecular structure visualization

### Experiment Tracking
- **Weights & Biases (wandb)** — experiment logging and visualization
- **MLflow** — experiment tracking and model management

## Common Workflow Pattern

```python
# 1. Load and prepare data
import pandas as pd
from rdkit import Chem
from rdkit.Chem import Descriptors, AllChem

df = pd.read_csv("data.csv")
mols = [Chem.MolFromSmiles(s) for s in df["smiles"]]

# 2. Compute representations
# Descriptors
descs = pd.DataFrame([{
    "MW": Descriptors.MolWt(m),
    "LogP": Descriptors.MolLogP(m),
    "TPSA": Descriptors.TPSA(m),
    "HBD": Descriptors.NumHDonors(m),
    "HBA": Descriptors.NumHAcceptors(m),
} for m in mols])

# Fingerprints
import numpy as np
fps = np.array([
    AllChem.GetMorganFingerprintAsBitVect(m, 2, nBits=2048)
    for m in mols
])

# 3. Split data (scaffold split)
from rdkit.Chem.Scaffolds import MurckoScaffold
# ... scaffold split implementation ...

# 4. Train and evaluate
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import mean_absolute_error, r2_score

model = RandomForestRegressor(n_estimators=500, random_state=42)
model.fit(X_train, y_train)
y_pred = model.predict(X_test)

print(f"MAE: {mean_absolute_error(y_test, y_pred):.3f}")
print(f"R²:  {r2_score(y_test, y_pred):.3f}")
```

## Installation

```bash
# Core stack
pip install pandas numpy scikit-learn matplotlib seaborn

# Chemistry
pip install rdkit  # or: conda install -c conda-forge rdkit

# Gradient boosting
pip install xgboost lightgbm

# Deep learning
pip install torch torchvision

# Graph neural networks
pip install torch-geometric

# Pretrained models
pip install transformers
```

## Tips

1. **Start with RDKit + scikit-learn.** This covers most chemistry ML needs without deep learning.
2. **Add PyTorch + PyG when you need GNNs** or other deep learning architectures.
3. **Use conda environments** to manage dependencies, especially for RDKit.
4. **Pin library versions** in your requirements file for reproducibility.
5. **Document your software stack** in every publication and shared project.
