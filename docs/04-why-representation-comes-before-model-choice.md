# Chapter 4 — Why Representation Comes Before Model Choice

## Part II — How to Represent Chemistry for a Machine

---

## Chemical Motivation

When a chemist looks at a molecule, they see a rich object: a three-dimensional arrangement of atoms connected by bonds, with electron density, polarity, flexibility, symmetry, and reactivity all encoded in the structure. A computer sees none of this. It sees numbers.

The act of converting a molecule into numbers — into a machine-readable representation — is one of the most consequential decisions in any chemistry ML project. It determines what information the model can access, what patterns it can detect, and what it is blind to. Choosing a model before choosing a representation is like choosing a detector before deciding what analyte you are measuring.

## Core Concept

A molecular representation is a translation of chemical structure into a format a computer can process. Different representations emphasize different aspects of the molecule:

- **Descriptors** reduce a molecule to a vector of computed properties (molecular weight, logP, polar surface area). They are compact but lossy — many structurally different molecules can have identical descriptor vectors.
- **Fingerprints** encode the presence or absence of substructural features as bit vectors. They preserve more structural detail than descriptors but still compress the molecule.
- **SMILES strings** represent molecules as text sequences. They preserve connectivity and atom identity but lose 3D information.
- **Molecular graphs** represent atoms as nodes and bonds as edges. They preserve the full connectivity of the molecule.
- **3D coordinates** add spatial information — bond lengths, angles, dihedral angles, and conformational shape.
- **Spectra** represent molecules through their experimental signatures — NMR peaks, IR absorption bands, mass fragments.
- **Images** represent molecules as 2D depictions or 3D renderings.

Each representation is a lens. It brings certain features into focus and blurs others. No single representation captures everything about a molecule.

## Chemist-Friendly Intuition

Think of molecular representations as different ways of describing a compound to a colleague:

- **Descriptors** are like saying: "It's a small, polar, flexible molecule with two hydrogen bond donors." You convey key properties but lose structural specificity.
- **Fingerprints** are like saying: "It contains a piperidine ring, an amide bond, and a trifluoromethyl group." You convey substructural features but not how they are connected.
- **SMILES** are like writing the IUPAC name — a precise, linear encoding of connectivity that a trained reader can decode back to the structure.
- **Graphs** are like drawing the structural formula — atoms and bonds in their relational arrangement.
- **3D coordinates** are like showing a physical model — the actual shape the molecule adopts in space.

The model only sees the representation, not the molecule. If you represent a chiral molecule using a 2D fingerprint that does not encode stereochemistry, the model cannot distinguish enantiomers. If you represent a flexible molecule using a single conformer, the model cannot account for conformational diversity.

This is not a limitation of the model — it is a limitation of the representation. The model is doing its best with what you give it.

### The Representation Determines the Ceiling

Here is a principle worth internalizing: **the best possible model performance is bounded by the information content of the representation.**

If the property you are trying to predict depends on 3D shape (e.g., protein–ligand binding), and your representation is a 2D fingerprint, then no amount of model sophistication can recover the missing 3D information. The representation sets the ceiling; the model tries to reach it.

This is why experienced practitioners spend more time thinking about representation than about model architecture. A good representation with a simple model often outperforms a poor representation with a complex model.

## Concrete Chemical Example

Consider predicting the aqueous solubility of drug-like molecules. You could represent each molecule as:

1. **A vector of physicochemical descriptors** — molecular weight, cLogP, TPSA, number of rotatable bonds, number of H-bond donors/acceptors. This captures bulk properties relevant to solubility but misses specific structural motifs.

2. **A Morgan fingerprint** — a bit vector encoding circular substructures of radius 2. This captures local structural environments but does not directly encode global properties like molecular weight.

3. **A SMILES string** — the canonical text representation. This preserves full connectivity but requires a model that can process sequences.

4. **A molecular graph** — atoms as nodes with features (element, charge, hybridization), bonds as edges with features (bond order, aromaticity). This preserves the full topology.

Each representation leads to a different modeling approach and potentially different performance. The descriptor-based approach might work well with a random forest. The fingerprint might work well with a nearest-neighbor model. The SMILES string requires a sequence model. The graph requires a graph neural network.

The choice is not arbitrary — it depends on what aspects of the molecule drive solubility, how much data you have, and what level of interpretability you need.

## What Can Go Wrong

- **Choosing a representation by habit rather than by reasoning.** Many chemists default to Morgan fingerprints because they are familiar, even when the problem calls for a different representation. Always ask: does this representation preserve the information my model needs?

- **Losing critical information.** If stereochemistry matters for your property (e.g., enantioselective binding), but your representation does not encode stereochemistry, the model is structurally blind to the relevant chemistry.

- **Overcomplicating the representation.** Using 3D coordinates when 2D connectivity suffices adds noise (conformer uncertainty) without adding useful signal. More information is not always better if the extra information is unreliable.

- **Ignoring the representation when diagnosing model failure.** When a model performs poorly, the first question should be: "Is the representation adequate?" not "Is the model powerful enough?"

## Practical Decision Rules

1. **Start by listing what chemical information matters for your property.** Does it depend on substructure? On global properties? On 3D shape? On electronic effects? This list guides your representation choice.

2. **Match representation complexity to data size.** Rich representations (graphs, 3D) require more data to learn from effectively. With small datasets, simpler representations (descriptors, fingerprints) often perform better.

3. **Consider combining representations.** You can concatenate descriptors with fingerprints, or use molecular graphs augmented with global descriptors. Multimodal representations can capture complementary information.

4. **Test multiple representations.** Just as you would screen reaction conditions, screen representations. Compare model performance across different representations on the same held-out data.

5. **Remember: the model sees only what you show it.** If important chemistry is missing from the representation, no model can compensate.

## Mini-Summary

Representation is the most important decision in chemistry ML. It determines what the model can see, what patterns it can learn, and what it is blind to. Different representations — descriptors, fingerprints, SMILES, graphs, 3D coordinates — emphasize different aspects of molecular structure. The representation sets the performance ceiling; the model tries to reach it. Choose your representation based on what chemical information matters for your problem, not based on habit or fashion.

## Exercises and Thought Questions

1. You want to predict whether a molecule will cross the blood–brain barrier. What chemical properties are relevant? Which representation(s) would best capture them?

2. Two molecules are enantiomers — identical in 2D connectivity but mirror images in 3D. Name three representations that would treat them as identical and one that could distinguish them.

3. A colleague argues that you should always use the most information-rich representation available (e.g., 3D coordinates). Under what circumstances might a simpler representation actually perform better?

4. You are predicting polymer glass transition temperatures. Your molecules are not small drug-like compounds but long-chain polymers. How does this change your representation choices?

5. Design a simple experiment to test whether 3D information improves model performance for a given property prediction task. What would you compare, and how would you ensure a fair comparison?
