# Appendix C — Chemistry ML Vocabulary

---

A glossary translating ML language into chemist-friendly explanations.

| ML Term | Chemistry Translation |
|---|---|
| **Feature** | A molecular descriptor, fingerprint bit, or other numerical property of a molecule |
| **Label / Target** | The measured property you want to predict (solubility, activity, yield) |
| **Training set** | The compounds the model learns from — your calibration standards |
| **Test set** | The compounds used for final evaluation — your independent validation |
| **Overfitting** | Memorizing the training compounds instead of learning transferable SAR |
| **Underfitting** | A model too simple to capture the structure–property relationship |
| **Regularization** | A constraint that prevents the model from memorizing noise |
| **Hyperparameter** | A model setting chosen by the user, not learned from data (like reaction temperature) |
| **Cross-validation** | Rotating the validation set to get a more robust performance estimate |
| **Epoch** | One complete pass through the training data during neural network training |
| **Batch** | A subset of training examples processed together in one update step |
| **Learning rate** | How large each parameter update is — like the step size in an optimization |
| **Loss function** | The measure of prediction error that the model minimizes during training |
| **Gradient descent** | The optimization method that adjusts parameters to reduce error |
| **Embedding** | A learned vector representation of a molecule from a neural network's hidden layer |
| **Latent space** | The internal coordinate system learned by a model, where molecules are positioned by learned features |
| **Attention** | A mechanism that allows the model to focus on the most relevant parts of the input |
| **Message passing** | The process in a GNN where atoms exchange information with their neighbors |
| **Readout / Pooling** | Combining atom-level features into a single molecular-level representation |
| **Fine-tuning** | Adapting a pretrained model to a specific task with a small dataset |
| **Transfer learning** | Using knowledge from one task to improve performance on another |
| **Ensemble** | A collection of models whose predictions are averaged for robustness |
| **Applicability domain** | The region of chemical space where the model's predictions are reliable |
| **Scaffold split** | A data split that ensures no scaffold appears in both training and test sets |
| **Data leakage** | When test set information contaminates the training process |
| **Acquisition function** | The scoring function in active learning that balances exploration and exploitation |
| **Autoregressive** | Generating output one piece at a time, each conditioned on previous pieces |
| **Equivariant** | A model property ensuring predictions behave correctly under rotations and translations |
| **SMILES** | Simplified Molecular Input Line Entry System — a text representation of molecular structure |
| **Fingerprint** | A bit vector encoding the substructural content of a molecule |
| **Descriptor** | A numerical value computed from molecular structure (MW, cLogP, TPSA, etc.) |
| **GNN** | Graph Neural Network — a model that learns directly from molecular graphs |
| **Transformer** | A neural network architecture using attention to process sequences or sets |
| **SHAP** | SHapley Additive exPlanations — a method for explaining individual predictions |
| **ROC-AUC** | Area under the receiver operating characteristic curve — a ranking metric for classifiers |
| **PR-AUC** | Area under the precision-recall curve — a ranking metric for imbalanced classifiers |
| **Enrichment factor** | How many times more actives are found in the top-ranked fraction compared to random |
