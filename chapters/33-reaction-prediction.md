# Chapter 33 — Reaction Prediction, Yield Prediction, and Retrosynthesis

---

## Chemical Motivation

Chemistry is not just about molecules — it is about transformations. Predicting what products a reaction will form, what yield it will achieve, and what route will lead to a target molecule are among the most impactful applications of ML in chemistry. These problems are also among the most challenging, because reactions depend on context — conditions, catalysts, solvents, concentrations, and timing all matter.

## Core Concept

### Reactions as Structured Data

A chemical reaction can be represented as structured data:
- **Reactants** — the starting materials (molecular structures)
- **Reagents/catalysts** — species that facilitate the reaction but are not consumed
- **Conditions** — temperature, solvent, time, concentration, atmosphere
- **Products** — the molecules formed
- **Yield** — the efficiency of the transformation
- **Selectivity** — the ratio of desired to undesired products

Each component can be represented using the molecular representations discussed earlier (SMILES, graphs, fingerprints), while conditions are typically represented as categorical or continuous features.

### Reaction Fingerprints

Reaction fingerprints extend molecular fingerprints to reactions. A common approach is the difference fingerprint: compute fingerprints for reactants and products separately, then take the difference. The resulting vector encodes what changed during the reaction — which substructures were consumed and which were formed.

### Sequence-Based Reaction Models

Reactions can be represented as SMILES strings: `reactants>>products`. A sequence-to-sequence model can then learn to "translate" reactant SMILES into product SMILES, treating reaction prediction as a language translation problem.

This approach has been remarkably successful for forward reaction prediction (given reactants and conditions, predict products) and for retrosynthesis (given a product, predict possible reactants).

### Yield Prediction

Yield prediction is a regression task: given reactants, conditions, and other context, predict the numerical yield. This is particularly challenging because yield depends on many factors that are difficult to capture in a model — mixing efficiency, reagent purity, operator skill, and subtle condition variations.

### Retrosynthesis

Retrosynthetic analysis asks: "Given a target molecule, what reactions could produce it?" ML-based retrosynthesis models propose disconnections — breaking the target into simpler precursors — and can be combined with search algorithms to plan multi-step synthetic routes.

## Chemist-Friendly Intuition

Reaction prediction is like asking a model to be a synthetic chemist. Given starting materials and conditions, what will you get? This requires understanding not just molecular structure but also reactivity, selectivity, and the influence of conditions.

Retrosynthesis is the reverse: given the target, work backward to find a route. This is how synthetic chemists plan syntheses — starting from the target and identifying key disconnections. ML models can automate this reasoning, proposing routes that a chemist can evaluate and refine.

## Concrete Chemical Example

You want to predict yields for Suzuki coupling reactions. Your dataset contains 5,000 reactions with:
- Aryl halide structure (SMILES)
- Boronic acid structure (SMILES)
- Catalyst (Pd source, ligand)
- Solvent
- Temperature
- Base
- Yield (0–100%)

A multimodal model processes:
1. Reactant structures through a GNN → molecular embeddings
2. Conditions through a dense network → condition embedding
3. Combined embeddings through a prediction network → predicted yield

The model achieves MAE = 12% yield on a held-out test set. This is useful for prioritizing conditions but not precise enough to replace experimental optimization.

## What Can Go Wrong

- **Condition dependence.** Yield depends heavily on conditions that are often poorly recorded or standardized. Missing or inconsistent condition data limits model performance.
- **Dataset bias.** Published reactions are biased toward successful outcomes. Failed reactions are rarely reported, creating a skewed training distribution.
- **Reproducibility.** The same reaction run by different chemists may give different yields. This noise limits the achievable model accuracy.
- **Retrosynthesis validation.** A proposed synthetic route may be chemically plausible but practically infeasible due to cost, availability, or safety constraints.

## Practical Decision Rules

1. **Include reaction conditions** as features — they are often as important as the molecular structures.
2. **Be realistic about yield prediction accuracy.** Experimental reproducibility sets a floor; models cannot be more accurate than the data.
3. **Validate retrosynthetic proposals** with experienced synthetic chemists before committing resources.
4. **Use scaffold or time splits** for reaction datasets to avoid leaking information from similar reactions.
5. **Consider the publication bias** in reaction databases — the absence of failed reactions biases models toward optimistic predictions.

## Mini-Summary

Reaction modeling extends ML from molecules to transformations. Forward prediction, yield prediction, and retrosynthesis are all active areas with significant practical impact. The key challenge is that reactions are contextual — conditions matter enormously, and data quality is often limited by inconsistent reporting and publication bias. Reaction ML is one of the most impactful and most challenging areas of chemistry ML.

## Exercises and Thought Questions

1. Represent a Diels-Alder reaction as a SMILES reaction string. What information is preserved? What is lost?
2. A yield prediction model achieves MAE = 8% on a random split but MAE = 18% on a time split. What does this tell you?
3. A retrosynthesis model proposes a route involving a reaction you have never seen. How would you evaluate its feasibility?
4. Why is publication bias a particular problem for reaction datasets? How might you mitigate it?
5. Compare a reaction fingerprint approach to a GNN-based approach for yield prediction. What are the trade-offs?
