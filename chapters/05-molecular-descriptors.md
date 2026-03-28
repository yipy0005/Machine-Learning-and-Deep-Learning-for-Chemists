# Chapter 5 — Molecular Descriptors: Hand-Crafted Chemical Summaries

---

## Chemical Motivation

Long before deep learning existed, chemists were building predictive models. The tool they used was the molecular descriptor — a numerical value computed from a molecular structure that captures some aspect of the molecule's chemistry. Molecular weight. LogP. Polar surface area. Hydrogen bond donor count. These are numbers that any chemist can interpret, and they have been the backbone of QSAR (quantitative structure–activity relationships) for decades.

Descriptors are the oldest and most interpretable form of molecular representation for ML. Understanding them is essential, both because they remain useful and because they illustrate fundamental principles about what it means to represent a molecule as numbers.

## Core Concept

A molecular descriptor is a function that takes a molecular structure as input and returns a number. That number summarizes some property of the molecule — its size, its polarity, its shape, its electronic character, its topology.

Descriptors can be grouped by what they capture:

### Constitutional Descriptors
These count basic structural features:
- Number of atoms, bonds, rings
- Molecular weight, molecular formula
- Number of rotatable bonds
- Number of hydrogen bond donors and acceptors

These are the simplest descriptors. They are fast to compute and easy to interpret, but they are coarse — many structurally different molecules share the same constitutional descriptors.

### Topological Descriptors
These capture the connectivity pattern of the molecular graph without considering 3D geometry:
- Wiener index (sum of shortest paths between all atom pairs)
- Balaban index (a measure of molecular branching)
- Kier–Hall connectivity indices
- Topological polar surface area (TPSA)

Topological descriptors encode how atoms are connected, which influences properties like flexibility, surface area, and transport.

### Physicochemical Descriptors
These estimate bulk physical or chemical properties:
- cLogP (computed octanol–water partition coefficient)
- Molar refractivity
- Aqueous solubility estimates
- pKa estimates

These descriptors directly relate to properties chemists care about and are often the most predictive for ADME (absorption, distribution, metabolism, excretion) endpoints.

### Electronic Descriptors
These capture electronic structure:
- Partial charges (Gasteiger charges, MMFF charges)
- Electronegativity-related indices
- HOMO/LUMO energies (from quantum chemical calculations)
- Dipole moment

Electronic descriptors are particularly relevant for reactivity prediction and for properties that depend on electron distribution.

### Shape and Surface Descriptors
These capture 3D molecular shape:
- Molecular volume
- Surface area (van der Waals, solvent-accessible)
- Globularity, asphericity
- Principal moments of inertia

Shape descriptors require a 3D conformer, which introduces conformer-dependence — the descriptor value changes depending on which conformer you use.

## Chemist-Friendly Intuition

Think of descriptors as a chemical passport for each molecule. Just as a passport reduces a person to a few key identifiers (name, date of birth, nationality, photo), descriptors reduce a molecule to a few key numbers. The passport is useful for many purposes — border control, identification, record-keeping — but it does not capture everything about the person. Similarly, descriptors are useful for many modeling tasks but do not capture everything about the molecule.

The power of descriptors lies in their interpretability. When a model says "compounds with high cLogP and low TPSA tend to be poorly soluble," a chemist can immediately understand and evaluate that statement. This transparency is valuable for building trust and for generating chemical hypotheses.

The limitation of descriptors is their compression. A descriptor vector of 200 numbers cannot fully represent the structural diversity of drug-like chemical space. Two molecules with identical descriptor vectors may have very different structures and very different activities. This is the descriptor collision problem — the chemical equivalent of a hash collision.

### Scaling and Collinearity

Descriptors often have very different scales. Molecular weight might range from 100 to 800, while the number of hydrogen bond donors ranges from 0 to 10. If you feed raw descriptors into a model that is sensitive to scale (like a linear model or a neural network), the model will be dominated by the large-scale descriptors.

The solution is feature scaling — normalizing each descriptor to a common range (e.g., zero mean and unit variance). This is analogous to normalizing concentrations in an assay to account for different stock solution concentrations.

Descriptors are also often correlated. Molecular weight correlates with the number of heavy atoms. cLogP correlates with the number of aromatic rings. This collinearity means that many descriptors carry redundant information. Some models handle collinearity gracefully (tree-based models), while others struggle (linear models without regularization).

## Concrete Chemical Example

Consider Lipinski's Rule of Five, one of the most famous descriptor-based models in medicinal chemistry. It uses just four descriptors to predict oral bioavailability:

- Molecular weight ≤ 500
- cLogP ≤ 5
- Hydrogen bond donors ≤ 5
- Hydrogen bond acceptors ≤ 10

This is, in essence, a simple decision tree built from four descriptors. It is interpretable, fast, and useful — but it is also approximate. Many orally bioavailable drugs violate one or more rules, and many compounds that satisfy all rules are not orally bioavailable.

The Rule of Five illustrates both the power and the limitation of descriptor-based approaches. With the right descriptors, even a simple model can capture useful trends. But descriptors are summaries, and summaries lose detail.

A more sophisticated descriptor-based model might use 200 descriptors with a random forest, capturing nonlinear interactions between descriptors that simple rules miss. For example, a compound with high molecular weight might still be soluble if it also has high polarity and few rotatable bonds — an interaction that a single-descriptor rule cannot express.

## What Can Go Wrong

- **Descriptor selection bias.** Choosing descriptors after seeing the data (e.g., selecting the 20 descriptors most correlated with the target) inflates apparent performance. Descriptor selection should be done within cross-validation, not before it.

- **Undefined or unreliable descriptors.** Some descriptors are undefined for certain molecule types. Others depend on the software used to compute them — different tools may give different values for the same descriptor. Always verify that your descriptors are computed consistently.

- **Over-reliance on interpretability.** Descriptors are interpretable, but that does not mean the model's use of them is chemically meaningful. A model might use molecular weight as a proxy for a more specific structural feature that happens to correlate with weight in the training set.

- **Ignoring 3D-dependent properties.** If the property you are predicting depends on molecular shape or conformation, 2D descriptors will miss critical information. Consider whether your descriptor set captures the relevant chemistry.

## Practical Decision Rules

1. **Start with a well-established descriptor set.** RDKit's descriptor module, Mordred, or PaDEL provide hundreds of validated descriptors. Do not reinvent the wheel.

2. **Scale your descriptors** before feeding them to scale-sensitive models (linear models, SVMs, neural networks). Tree-based models are scale-invariant and do not require scaling.

3. **Remove constant and near-constant descriptors.** A descriptor that has the same value for all compounds carries no information.

4. **Handle missing values explicitly.** If a descriptor is undefined for some molecules, decide whether to impute, drop the descriptor, or drop the molecules — and document your choice.

5. **Use descriptors when interpretability matters** and when your dataset is small to moderate. Descriptors work well with classical ML models and require less data than learned representations.

## Mini-Summary

Molecular descriptors are hand-crafted numerical summaries of molecular structure. They are interpretable, fast to compute, and effective for many chemistry ML tasks. Their limitation is compression — they reduce a complex molecule to a fixed-length vector, inevitably losing structural detail. Descriptors remain a strong first choice for many problems, especially when data is limited and interpretability is valued. They are the foundation on which modern molecular representations were built.

## Exercises and Thought Questions

1. Compute five descriptors (molecular weight, cLogP, TPSA, number of H-bond donors, number of rotatable bonds) for aspirin, ibuprofen, and caffeine. How well do these five numbers distinguish the three molecules?

2. Two molecules have identical values for all 200 RDKit descriptors but different structures. Is this possible? What would it mean for a descriptor-based model?

3. You are building a model to predict blood–brain barrier penetration. Which descriptor categories (constitutional, topological, physicochemical, electronic, shape) would you prioritize, and why?

4. A colleague reports that their model uses 500 descriptors and achieves excellent cross-validated performance. You notice they selected the 500 descriptors based on correlation with the target before cross-validation. What is the problem?

5. When would you choose descriptors over fingerprints? When would you choose fingerprints over descriptors?
