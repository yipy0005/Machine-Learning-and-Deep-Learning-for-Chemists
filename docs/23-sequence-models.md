# Chapter 23 — Sequence Models for Chemical Strings

## Part VI — Deep Learning by Chemical Representation

---

## Chemical Motivation

If molecules can be written as SMILES strings, then chemistry becomes a language — and the powerful tools of natural language processing become available. Sequence models process SMILES character by character (or token by token), learning the grammar of valid molecules and the semantics of chemical properties. This approach has produced some of the most impressive results in molecular generation, reaction prediction, and property prediction.

## Core Concept

### Token Sequences

A SMILES string is first tokenized — broken into individual units that the model processes sequentially. The model reads the sequence from left to right, building up an internal representation of the molecule as it goes.

This is analogous to how a chemist reads a structural formula: starting from one end, following the bonds, noting branches and ring closures, and building a mental picture of the molecule.

### Recurrent Models

Recurrent neural networks (RNNs) process sequences by maintaining a hidden state that is updated at each token. The hidden state acts as a memory — it accumulates information about the tokens seen so far. After processing the entire sequence, the final hidden state encodes a summary of the molecule.

Think of the hidden state as a running tally. As the model reads each token, it updates its understanding of the molecule. By the end, it has a compressed representation that captures the essential features of the SMILES string.

### Attention and Transformers

The attention mechanism allows the model to focus on specific parts of the sequence when making predictions, rather than relying solely on the compressed hidden state. When predicting a property, the model can "attend" to the tokens most relevant to that property — perhaps focusing on a functional group or a ring system.

Transformers extend this idea by processing all tokens simultaneously (rather than sequentially) and using attention to capture relationships between any pair of tokens in the sequence. This parallel processing makes transformers faster to train and better at capturing long-range dependencies — relationships between tokens far apart in the sequence.

In chemical terms, a transformer can learn that a substituent at one end of a molecule affects the properties of a functional group at the other end, even if they are separated by many tokens in the SMILES string.

### Pretraining on Large Molecular Corpora

One of the most powerful applications of sequence models is pretraining. A model is first trained on millions of unlabeled SMILES strings to learn the grammar and statistics of molecular language. This pretrained model is then fine-tuned on a smaller labeled dataset for a specific property prediction task.

Pretraining is like a chemistry education. The model first learns what valid molecules look like (general chemistry knowledge), then specializes in a specific property (graduate-level expertise). The pretrained knowledge helps the model learn from fewer labeled examples.

## Chemist-Friendly Intuition

A sequence model reads a SMILES string the way a chemist reads a structural formula — building up understanding token by token. The attention mechanism is like the chemist's ability to focus on the most relevant parts of the structure when making a prediction.

Pretraining is like the difference between teaching chemistry to a student who has never seen a molecule and teaching it to a student who has already studied organic chemistry. The pretrained student learns faster because they already understand the basics.

## Concrete Chemical Example

**Property prediction:** You fine-tune a pretrained transformer (e.g., ChemBERTa) on 2,000 compounds with measured solubility. The pretrained model already understands SMILES grammar and common molecular patterns. Fine-tuning teaches it to map these patterns to solubility. Despite the small dataset, the model performs well because pretraining provided a strong foundation.

**Molecule generation:** You train an autoregressive model (one that generates SMILES one token at a time) on a large database of drug-like molecules. The model learns to produce valid SMILES strings that resemble the training distribution. You can then bias the generation toward molecules with desired properties using reinforcement learning or conditional generation.

**Reaction prediction:** You represent reactions as SMILES strings (reactants >> products) and train a sequence-to-sequence model. Given reactant SMILES, the model generates product SMILES. This treats reaction prediction as a translation problem — translating from the language of reactants to the language of products.

## What Can Go Wrong

- **SMILES syntax errors in generation.** Generated SMILES may be invalid (unbalanced parentheses, valence violations). Post-processing filters are needed.
- **Sensitivity to tokenization.** Different tokenization schemes can affect model performance. Atom-level tokenization generally outperforms character-level for chemistry.
- **Non-uniqueness of SMILES.** The same molecule has many valid SMILES. The model may treat different SMILES of the same molecule as different inputs. Data augmentation with randomized SMILES can help.
- **Loss of structural information.** SMILES linearize the molecular graph, which can obscure structural relationships that are obvious in the graph representation.

## Practical Decision Rules

1. **Use pretrained sequence models** when labeled data is scarce. Pretraining on large molecular databases provides a strong foundation.
2. **Use data augmentation with randomized SMILES** to improve generalization.
3. **Validate generated SMILES** by parsing them and checking chemical validity.
4. **Compare to graph-based models** for property prediction. Sequence models and graph models often achieve similar performance, and the best choice depends on the specific task.
5. **Use sequence models for generation and reaction prediction,** where the sequential nature of SMILES is a natural fit.

## Mini-Summary

Sequence models process SMILES strings token by token, treating chemistry as a language problem. Attention mechanisms and transformers capture relationships between distant parts of the molecule. Pretraining on large molecular corpora provides a strong foundation for downstream tasks. Sequence models are particularly powerful for molecular generation and reaction prediction, where the sequential nature of SMILES is a natural fit.

## Exercises and Thought Questions

1. Tokenize the SMILES string for caffeine at the character level and at the atom level. How do the token sequences differ?
2. A pretrained model achieves better solubility prediction than a model trained from scratch on the same data. Why? What did pretraining provide?
3. Generate 100 SMILES strings from a trained generative model. What fraction are valid? What fraction represent drug-like molecules?
4. Compare a transformer-based model and a GNN on the same property prediction task. Which performs better? Does the answer depend on the dataset size?
5. A sequence model predicts that a reaction will produce a specific product. How would you validate this prediction before running the reaction?
