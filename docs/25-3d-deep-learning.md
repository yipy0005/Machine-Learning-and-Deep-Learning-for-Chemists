# Chapter 25 — 3D Deep Learning and Geometry-Aware Models

---

## Chemical Motivation

When a drug binds to a protein, it is the 3D shape that matters — the spatial arrangement of atoms, the geometry of hydrogen bonds, the complementarity of surfaces. When a catalyst controls stereoselectivity, it is the 3D environment around the active site that determines the outcome. For these problems, 2D graphs are insufficient. We need models that understand geometry.

## Core Concept

### Coordinates and Geometric Relationships

3D deep learning models take atomic coordinates as input and learn from geometric relationships: distances between atoms, angles between bonds, and dihedral angles along chains. Unlike 2D graphs that encode only connectivity, 3D models encode spatial arrangement.

The input is typically a set of atoms, each with a position in 3D space (x, y, z coordinates) and associated features (element type, charge, etc.). The model learns to extract patterns from the spatial arrangement of these atoms.

### Distance-Based Models

The simplest 3D models use interatomic distances as features. SchNet, for example, computes continuous filters based on the distance between atom pairs, allowing the model to learn distance-dependent interactions. This is chemically intuitive — many molecular interactions (van der Waals, electrostatic, hydrogen bonding) depend on distance.

### Equivariance Intuition

A key concept in 3D deep learning is equivariance: the model's predictions should behave correctly under rotations and translations of the molecule. If you rotate a molecule in space, its properties do not change — solubility, binding affinity, and energy are all invariant to orientation. A good 3D model should respect this.

Equivariant models are designed so that rotating the input coordinates produces a correspondingly rotated internal representation, while the final scalar prediction (energy, affinity) remains unchanged. This is not just a mathematical nicety — it dramatically reduces the amount of data needed for training because the model does not need to learn rotational invariance from examples.

Think of it this way: a non-equivariant model would need to see a molecule in many different orientations to learn that orientation does not matter. An equivariant model knows this by construction, freeing it to focus on learning the actual chemistry.

### Applications

**Quantum property prediction:** Predicting molecular energies, forces, and electronic properties from 3D coordinates. Models like SchNet, DimeNet, and PaiNN have achieved remarkable accuracy on quantum chemistry benchmarks.

**Conformer-sensitive tasks:** Properties that depend on molecular shape, such as protein–ligand binding affinity, crystal packing energy, or optical rotation.

**Structure-based drug design:** Generating or scoring molecules in the context of a protein binding pocket, where the 3D arrangement of both protein and ligand determines binding.

## Chemist-Friendly Intuition

A 3D model sees the molecule the way you see a physical model — as a collection of atoms arranged in space. It can learn that two atoms 3 Å apart interact differently than two atoms 5 Å apart, that a hydrogen bond requires a specific geometry, and that steric clashes occur when atoms are too close.

Equivariance is like the principle that a molecule's properties do not depend on how you hold the model. Whether you rotate it, flip it, or translate it across the room, the molecule is the same. Equivariant models encode this principle directly.

## Concrete Chemical Example

You want to predict the binding affinity of small molecules to a protein kinase. You have crystal structures of 500 protein–ligand complexes with measured binding affinities.

1. **Input:** For each complex, extract the 3D coordinates of the ligand and the protein binding pocket (atoms within 6 Å of the ligand).
2. **Model:** Train an equivariant GNN (e.g., EGNN or PaiNN) that processes the 3D point cloud of atoms, learning distance-dependent and angle-dependent interactions.
3. **Output:** Predicted binding affinity (pKd).

The model learns that certain geometric arrangements — a hydrogen bond donor at 2.8 Å from an acceptor, a hydrophobic contact at 3.5 Å — are associated with strong binding. These are the same interactions a structural biologist would identify, but learned from data rather than programmed.

## What Can Go Wrong

- **Conformer sensitivity.** Predictions depend on the input conformer. If the conformer is wrong, the prediction is wrong.
- **Computational cost.** 3D models are more expensive than 2D models, especially for large molecules or protein–ligand complexes.
- **Data requirements.** 3D models have more parameters and need more data. With small datasets, 2D models often generalize better.
- **Coordinate uncertainty.** Experimental coordinates (from X-ray crystallography) have resolution limits. Computed coordinates (from force fields or docking) are approximate. The model may learn artifacts of the coordinate generation method.

## Practical Decision Rules

1. **Use 3D models when geometry drives the property** — binding affinity, conformational energy, stereochemistry-dependent properties.
2. **Start with 2D models and add 3D only if 2D is insufficient.** The added complexity must be justified by improved performance.
3. **Use equivariant architectures** to avoid wasting data on learning rotational invariance.
4. **Be explicit about conformer generation.** Document how coordinates were obtained and test sensitivity to conformer choice.
5. **Consider the data size.** 3D models typically need more training data than 2D models.

## Mini-Summary

3D deep learning models process atomic coordinates to learn geometry-dependent patterns — distances, angles, and spatial arrangements that drive molecular properties. Equivariant architectures respect the physical symmetries of molecules, reducing data requirements. These models are essential when shape and spatial arrangement matter but add complexity and computational cost. Use them deliberately, when 2D representations are demonstrably insufficient.

## Exercises and Thought Questions

1. Name three molecular properties that require 3D information and three that can be predicted from 2D structure alone.
2. Why is equivariance important for 3D molecular models? What happens if a model is not equivariant?
3. You train a 3D model on DFT-optimized geometries but deploy it on force-field-minimized geometries. What problems might arise?
4. Compare a 3D equivariant GNN to a 2D GNN for predicting binding affinity. When does the 3D model win?
5. How would you handle conformational flexibility in a 3D model? What are the trade-offs between using a single conformer and an ensemble?
