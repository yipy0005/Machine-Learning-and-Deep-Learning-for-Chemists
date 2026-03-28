# Appendix D — Common Molecular Representations at a Glance

---

| Representation | What It Encodes | Typical Size | Strengths | Limitations | Best For |
|---|---|---|---|---|---|
| **Descriptors** | Computed molecular properties | 50–2000 numbers | Interpretable, fast, compact | Lossy, may miss structural detail | Small datasets, interpretable models |
| **Fingerprints** | Substructural features as bits | 1024–4096 bits | Fast, good for similarity, well-established | Hash collisions, no global properties | Similarity search, classical ML |
| **SMILES** | Molecular connectivity as text | 20–200 characters | Compact, enables language models | Non-unique, syntax-fragile, no 3D | Sequence models, generation |
| **Molecular graphs** | Atoms (nodes) and bonds (edges) | 10–100 nodes | Preserves topology, chemistry-native | No 3D information | GNNs, topology-dependent properties |
| **3D coordinates** | Atomic positions in space | 3N numbers (N atoms) | Captures shape and geometry | Conformer-dependent, expensive | Binding affinity, stereochemistry |
| **Spectra** | Experimental signals (NMR, IR, MS) | 100–10000 points | Direct experimental data | Instrument-dependent, preprocessing needed | Spectral interpretation, compound ID |
| **Multimodal** | Multiple representations combined | Variable | Captures complementary information | Complex, more data needed | Reactions, protein–ligand, conditions |

## Choosing a Representation

1. What chemical information matters for your property?
2. How much data do you have? (Simpler representations for smaller datasets)
3. What model will you use? (Representation must match model input format)
4. Do you need interpretability? (Descriptors > fingerprints > graphs > 3D)
5. Is 3D information necessary? (Only if geometry drives the property)
