# Appendix E — Common Model Families at a Glance

---

| Model Family | Input Type | Strengths | Limitations | Typical Use Case | Data Needed |
|---|---|---|---|---|---|
| **Linear models** | Descriptors, FP | Interpretable, fast, stable | Cannot capture interactions | Baseline, interpretable QSAR | 50+ |
| **k-Nearest neighbors** | Any (with distance metric) | Simple, natural applicability domain | No generalization beyond training data | Baseline, similarity search | 100+ |
| **Decision trees** | Descriptors, FP | Interpretable, captures interactions | Overfits easily (single tree) | Interpretable rules | 100+ |
| **Random forests** | Descriptors, FP | Robust, handles interactions, fast | Cannot extrapolate, limited to tabular data | Default for tabular chemistry data | 200+ |
| **Gradient boosting** | Descriptors, FP | Often best on tabular data | More tuning needed, cannot extrapolate | Best performance on tabular data | 500+ |
| **SVMs** | Descriptors, FP | Good with kernels, resistant to overfitting | Slow on large data, limited interpretability | Small datasets, kernel-based similarity | 100+ |
| **Dense neural networks** | Descriptors, FP | Learns feature combinations | Needs more data, less interpretable | Bridge from classical ML to deep learning | 1000+ |
| **GNNs** | Molecular graphs | Learns from topology directly | Needs more data, more complex | Topology-dependent properties | 1000+ |
| **Sequence models** | SMILES | Leverages language model advances | SMILES quirks, syntax errors in generation | Generation, reaction prediction | 1000+ |
| **Transformers** | SMILES, graphs, any | Powerful, scalable, pretrained models available | Data-hungry, expensive to train | Large-scale prediction, pretraining | 5000+ |
| **3D models** | Coordinates | Captures geometry | Conformer-dependent, expensive | Binding affinity, quantum properties | 1000+ |
| **CNNs** | Images, spectra, grids | Detects spatial/signal patterns | Needs spatial/signal data | Spectral analysis, microscopy | 500+ |

## Quick Decision Guide

1. **< 500 compounds, descriptors:** Random forest or gradient boosting
2. **500–5000 compounds, fingerprints:** Gradient boosting or dense neural network
3. **> 5000 compounds, graphs:** GNN (compare to fingerprint baseline)
4. **Generation task:** Sequence model or VAE
5. **3D-dependent property:** Equivariant GNN
6. **Spectral data:** CNN
7. **Multiple data types:** Multimodal architecture
