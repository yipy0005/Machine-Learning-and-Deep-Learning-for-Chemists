# Chapter 2 — Chemical Data: Measurements, Noise, and Reality

---

## Chemical Motivation

Before any model is trained, before any algorithm is chosen, there is data. In chemistry, data is not abstract — it comes from real experiments performed by real people in real laboratories. A solubility measurement reflects a specific protocol, a specific temperature, a specific purity of compound. An IC50 value depends on the cell line, the assay format, the incubation time, and the skill of the technician.

Every chemist knows that repeating an experiment does not always give the same number. Yet when we hand data to a machine learning model, we often treat each value as if it were exact truth. This chapter is about confronting that gap — understanding what chemical data really is, where it comes from, and why its imperfections matter profoundly for everything that follows.

## Core Concept

In machine learning, data consists of examples. Each example has:

- **Features** (also called inputs or independent variables) — the description of the chemical entity. This might be a set of molecular descriptors, a fingerprint, a SMILES string, or a graph.
- **Labels** (also called targets or dependent variables) — the measured outcome. This might be a solubility value, a binding affinity, a yield, or a binary classification (active/inactive).

The model learns a mapping from features to labels. If the labels are wrong, noisy, or inconsistent, the model learns a wrong, noisy, or inconsistent mapping.

Think of it like a calibration curve. If your standards are contaminated, your calibration is off, and every measurement you derive from it is unreliable. In ML, the training data is your calibration set. Its quality determines the ceiling of what any model can achieve.

## Chemist-Friendly Intuition

### What Counts as Data in Chemistry

Chemical data comes in many forms:

- **Molecules** — described by structure (SMILES, InChI, connection tables), computed properties, or experimental measurements.
- **Reactions** — described by reactants, products, reagents, catalysts, solvents, temperatures, and yields.
- **Spectra** — NMR, IR, UV-Vis, mass spectrometry, Raman — each a signal that encodes structural or compositional information.
- **Sequences** — amino acid sequences for proteins, nucleotide sequences for nucleic acids.
- **Simulations** — molecular dynamics trajectories, DFT-computed energies, docking scores.
- **Images** — microscopy images, crystallographic data, plate reader heatmaps.
- **Metadata** — assay conditions, batch identifiers, dates, lab identifiers, instrument settings.

Each type of data has its own noise characteristics, its own biases, and its own limitations.

### Experimental Error and Irreproducibility

Consider aqueous solubility. A landmark study by Llinas and Avdeef compared solubility values for the same compounds measured by different laboratories. The disagreement was striking — for some compounds, reported values differed by more than an order of magnitude.

This is not unusual in chemistry. Sources of variability include:

- **Protocol differences** — shake-flask vs. nephelometry vs. kinetic solubility assays measure different things and give different numbers.
- **Compound purity** — impurities can dramatically affect measured properties.
- **Polymorphism** — different crystal forms of the same compound can have different solubilities.
- **Temperature and pH** — small differences in conditions produce real differences in measurements.
- **Human factors** — pipetting precision, timing, sample handling.

When two labs report different solubility values for the same compound, which one is "true"? Often, both are valid measurements under their respective conditions, but neither is a universal truth.

For a machine learning model, this means the labels it learns from are inherently uncertain. A model that predicts solubility to within 0.5 log units may already be approaching the experimental noise floor. Demanding higher accuracy from the model than the data can support is like demanding a balance to weigh to micrograms when your samples are hygroscopic.

### Missing Values

Chemical datasets are rarely complete. A compound may have been tested in one assay but not another. A reaction may have yield data but no selectivity data. A descriptor may be undefined for certain molecular types (e.g., aromaticity descriptors for purely aliphatic compounds).

Missing values are not random. They often reflect experimental choices — compounds that were not tested because they were deemed uninteresting, reactions that were not attempted because the chemist expected them to fail. This non-random missingness can bias models in subtle ways.

### Batch Effects

In high-throughput screening, compounds tested on different plates, on different days, or by different operators may show systematic differences unrelated to the chemistry. These batch effects are a form of confounding — the model may learn to distinguish batches rather than chemical properties.

Batch effects are the ML equivalent of a systematic error in an analytical method. They do not average out with more data; they persist and mislead.

### Inter-Laboratory Variability

When datasets are assembled from multiple sources — as is common in public databases like ChEMBL — the same endpoint may have been measured under different conditions by different groups. Merging these data without accounting for protocol differences introduces noise that no model can resolve.

This is analogous to combining titration results from labs using different indicators and different endpoint criteria. The numbers look comparable, but they are not directly commensurable.

## Concrete Chemical Example

Suppose you are building a model to predict hERG channel inhibition (a cardiac safety liability) for drug candidates. You assemble data from ChEMBL, collecting IC50 values for compounds tested against the hERG channel.

You quickly discover:

- Some values come from patch-clamp electrophysiology (the gold standard), others from fluorescence-based assays (faster but less reliable).
- IC50 values are reported at different holding potentials, different temperatures, and different compound concentrations.
- Some entries report exact IC50 values; others report only "> 10 μM" (censored data — you know the compound is weakly active, but not exactly how weakly).
- Duplicate measurements for the same compound sometimes disagree by 3-fold or more.

If you train a model on this data without careful curation, the model will learn a blurred, inconsistent version of hERG inhibition. Its predictions will reflect the noise in the data, not the true structure–activity relationship.

A careful chemist would:

1. Filter for a single, well-defined assay protocol where possible.
2. Handle censored data explicitly (not by treating "> 10 μM" as exactly 10 μM).
3. Average or median-aggregate duplicate measurements.
4. Document which data sources were included and which were excluded, and why.

This curation step is not glamorous, but it is often the single most impactful thing you can do for model quality.

## What Can Go Wrong

- **Treating all data as equally reliable.** A model trained on a mix of high-quality and low-quality measurements will be dragged down by the low-quality data. Consider weighting or filtering by data quality.
- **Ignoring censored data.** Many biological assays produce censored values (e.g., "> 30 μM" or "< 1 nM"). Treating these as exact values introduces systematic bias. Specialized methods exist for handling censored data.
- **Merging incompatible datasets.** Combining data from different assay formats without harmonization is like averaging Celsius and Fahrenheit temperatures — the numbers are meaningless.
- **Assuming labels are ground truth.** In chemistry, labels are measurements, and measurements have uncertainty. A model that perfectly reproduces noisy labels has memorized noise, not learned chemistry.
- **Neglecting metadata.** Information about assay conditions, dates, operators, and instruments is often discarded. This metadata can reveal batch effects, temporal trends, and other confounds.

## Practical Decision Rules

1. **Audit your data before modeling.** Examine distributions, check for duplicates, look for outliers, and understand the provenance of each measurement. This is the equivalent of characterizing your starting materials before running a reaction.

2. **Quantify label noise.** If replicate measurements exist, compute the standard deviation. This gives you a noise floor — the best any model can do. If your model's error is close to the noise floor, it may already be performing optimally.

3. **Be explicit about data exclusions.** Document what you removed and why. Future users of your model (including your future self) need to know.

4. **Prefer homogeneous data.** A smaller dataset from a single, well-controlled assay is often more valuable than a larger dataset assembled from heterogeneous sources.

5. **Preserve metadata.** Even if you do not use assay conditions as features, keep them available for diagnosing unexpected model behavior.

## Mini-Summary

Chemical data is not clean, not complete, and not perfectly reproducible. Every measurement carries uncertainty from experimental conditions, protocols, and human factors. A machine learning model can never be better than the data it learns from. The most important — and most underappreciated — step in any chemistry ML project is understanding, curating, and honestly characterizing the data. Treat your training data with the same care you would treat your analytical standards.

## Exercises and Thought Questions

1. Find a public dataset for a property you care about (e.g., solubility, logP, or a biological activity in ChEMBL). How many unique compounds does it contain? How many have duplicate measurements? What is the typical disagreement between duplicates?

2. You have IC50 values from two different assay formats for the same target. One uses a biochemical assay, the other a cell-based assay. Should you combine them into a single training set? What are the risks?

3. A dataset contains 10,000 compounds, but 2,000 of them have the label "> 100 μM." How would you handle these censored values? What happens if you simply drop them?

4. Your model achieves an RMSE of 0.6 log units on a solubility prediction task. The experimental reproducibility of the assay is ±0.5 log units. Is your model performing well or poorly? How would you decide?

5. Think about a dataset you have worked with. What metadata was available? What metadata was missing? How might the missing metadata affect a model trained on that data?
