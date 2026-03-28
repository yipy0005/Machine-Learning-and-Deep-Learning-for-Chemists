# Chapter 26 — Convolutional Models for Chemical Images, Spectra, and Grids

---

## Chemical Motivation

Not all chemistry data comes as molecules. Microscopy images reveal cell morphology. Spectra encode structural information as signals. Electron density maps represent molecular shape on grids. For these data types, convolutional neural networks (CNNs) — the workhorses of image recognition — are the natural modeling tool.

## Core Concept

Convolutional neural networks detect local patterns in spatial or signal data. A CNN slides a small filter (kernel) across the input, computing a response at each position. The filter learns to detect specific patterns — edges in images, peaks in spectra, density features in grids.

Multiple filters detect multiple patterns. Stacking convolutional layers allows the network to build hierarchical representations: early layers detect simple patterns (edges, peaks), later layers combine them into complex features (ring systems, functional groups, binding motifs).

### Images in Chemistry

- **Microscopy images** — cell painting assays produce images of cells treated with compounds. CNNs can learn to predict compound activity or mechanism of action from cell morphology changes.
- **Molecular images** — 2D depictions of molecules can be processed by CNNs, though this is generally less effective than graph-based approaches.
- **Crystallographic images** — electron density maps and diffraction patterns contain structural information that CNNs can extract.

### Spectral Data

- **NMR spectra** — 1D or 2D spectra can be processed by 1D or 2D CNNs to predict structure, identify functional groups, or classify compounds.
- **IR and Raman spectra** — absorption or scattering patterns encode functional group information that CNNs can learn to interpret.
- **Mass spectra** — fragmentation patterns can be processed as 1D signals or as sets of peaks.
- **UV-Vis spectra** — absorption profiles relate to electronic structure and can be modeled with CNNs.

### Voxelized Representations

Protein binding pockets can be represented as 3D grids (voxels), where each voxel encodes the presence and type of atoms. 3D CNNs can process these grids to predict binding affinity or identify favorable binding sites. This is like applying image recognition to a 3D molecular image.

## Chemist-Friendly Intuition

A CNN is like a chemist scanning a spectrum with a trained eye. The chemist looks for characteristic patterns — a broad O-H stretch in IR, a singlet in NMR, a specific fragmentation pattern in mass spec. The CNN does the same thing, but it learns which patterns are informative from the data rather than from a textbook.

The hierarchical nature of CNNs mirrors how chemists interpret spectra: first identify individual peaks (local patterns), then combine them into functional group assignments (intermediate patterns), then assemble a structural hypothesis (global interpretation).

## Concrete Chemical Example

You have IR spectra for 5,000 compounds and want to predict whether each compound contains a carboxylic acid group. A 1D CNN processes each spectrum:

1. The first convolutional layer detects local spectral features — peaks, shoulders, and baseline variations.
2. The second layer combines these into higher-level patterns — the broad O-H stretch around 2500–3300 cm⁻¹ combined with the C=O stretch around 1710 cm⁻¹.
3. A final dense layer combines these patterns into a binary prediction: carboxylic acid present or absent.

The model achieves 95% accuracy, and examining the learned filters reveals that the model has independently discovered the spectral signatures that chemists use to identify carboxylic acids.

## What Can Go Wrong

- **Resolution and preprocessing sensitivity.** Spectral preprocessing (baseline correction, normalization, binning) affects model performance. Document and standardize preprocessing.
- **Overfitting on small spectral datasets.** CNNs have many parameters. With small datasets, simpler approaches (peak picking + classical ML) may work better.
- **Loss of chemical context.** A CNN processing a spectrum does not know the molecular structure. Combining spectral and structural information (multimodal learning, Chapter 27) can improve predictions.

## Practical Decision Rules

1. **Use CNNs when your data is naturally spatial or signal-like** — images, spectra, grids.
2. **Standardize preprocessing** and document it carefully.
3. **Compare to classical approaches** (peak picking, spectral matching) before committing to deep learning.
4. **Consider 3D CNNs for voxelized molecular data** but be aware of the high computational cost.
5. **Combine spectral models with structural models** for the best of both worlds.

## Mini-Summary

Convolutional neural networks detect local patterns in spatial and signal data, making them natural tools for chemical images, spectra, and voxelized representations. They learn hierarchical features — from simple patterns to complex signatures — without manual feature engineering. CNNs are most useful when chemistry is expressed as spatial or signal-like data rather than as molecular graphs.

## Exercises and Thought Questions

1. How would you preprocess an IR spectrum before feeding it to a CNN? What information might be lost or preserved?
2. Compare a CNN on molecular images to a GNN on molecular graphs for property prediction. Which would you expect to perform better, and why?
3. A 3D CNN on voxelized protein pockets achieves good binding affinity prediction. What are the advantages and disadvantages compared to a point-cloud-based 3D GNN?
4. You have NMR spectra and molecular structures for 1,000 compounds. How would you combine both information sources in a single model?
5. When would a classical spectral analysis approach outperform a CNN?
