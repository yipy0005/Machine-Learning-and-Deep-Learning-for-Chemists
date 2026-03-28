# Machine Learning and Deep Learning for Chemists

**A chemistry-first guide from basic concepts to modern models**

This book teaches machine learning and deep learning using chemical language, chemical intuition, and chemical examples — while keeping mathematics supportive rather than dominant. Every ML concept is introduced through the lens of chemistry: titrations for optimization, calibration curves for training data, reaction conditions for hyperparameters, and SAR for pattern learning.

## What's Inside

| Part | Chapters | Covers |
|---|---|---|
| **I** — Thinking in ML as a Chemist | 1–3 | What ML is, chemical data reality, framing tasks |
| **II** — Representing Chemistry | 4–9 | Descriptors, fingerprints, SMILES, graphs, 3D |
| **III** — Classical ML | 10–14 | Baselines, linear models, trees, forests, SVMs |
| **IV** — Evaluating Like a Scientist | 15–19 | Splits, metrics, overfitting, interpretability |
| **V** — Neural Networks | 20–22 | Dense nets, learned representations, latent space |
| **VI** — Deep Learning by Representation | 23–27 | Sequence models, GNNs, 3D, CNNs, multimodal |
| **VII** — Advanced Concepts | 28–35 | Transfer learning, uncertainty, active learning, generation, reactions, protein–ligand, simulation |
| **VIII** — Scientific Judgment | 36–40 | Model choice, failure modes, reproducibility, paper reading, end-to-end workflow |
| **IX** — Case Studies | 41–45 | Solubility, bioactivity, reaction yield, active learning, first GNN |
| **Appendices** | A–G | Minimal math, statistics, vocabulary, reference tables, Python stack |

Plus a **Grand Decision Workflow** — a single-page reference with decision trees, checklists, and cheat sheets for chemists who need answers fast.

## Who This Book Is For

- Experimental chemists wanting to understand ML
- Computational chemists with limited ML background
- Graduate students and advanced undergraduates in chemistry
- Medicinal chemists, materials chemists, chemical biologists
- ML practitioners entering chemistry

**Assumed background:** Chemistry knowledge and comfort with data tables. No advanced math, statistics, or CS required.

## Serving the Documentation Locally

The book is built with [MkDocs](https://www.mkdocs.org/) and the [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) theme. Dependencies are managed with [pixi](https://pixi.sh/).

### Prerequisites

Install pixi if you don't have it:

```bash
# macOS / Linux
curl -fsSL https://pixi.sh/install.sh | bash

# or with Homebrew
brew install pixi
```

### Quick Start

```bash
# Clone the repository
git clone git@github.com:yipy0005/Machine-Learning-and-Deep-Learning-for-Chemists.git
cd Machine-Learning-and-Deep-Learning-for-Chemists

# Install dependencies (pixi handles everything)
pixi install

# Serve the docs locally
pixi run mkdocs serve
```

Open [http://127.0.0.1:8000](http://127.0.0.1:8000) in your browser. The site auto-reloads when you edit any file.

### Building a Static Site

To generate a static HTML site (e.g., for hosting):

```bash
pixi run mkdocs build
```

The output goes to the `site/` directory. You can serve it with any static file server or deploy it to GitHub Pages, Netlify, etc.

### Deploying to GitHub Pages

```bash
pixi run mkdocs gh-deploy
```

This builds the site and pushes it to the `gh-pages` branch automatically.

## Project Structure

```
.
├── book.md                  # Original book blueprint / outline
├── mkdocs.yml               # MkDocs configuration and navigation
├── pixi.toml                # Pixi dependency manifest
├── pixi.lock                # Locked dependency versions
├── chapters/                # Source markdown files (canonical copies)
│   ├── 00-front-matter.md
│   ├── 01-what-machine-learning-is.md
│   ├── ...
│   ├── 45-case-study-gnn.md
│   └── appendix-a-minimal-math.md ... appendix-g-python-stack.md
└── docs/                    # MkDocs source directory (served content)
    ├── index.md             # Home page (front matter)
    ├── decision-workflow.md # Grand Decision Workflow
    ├── 01-what-machine-learning-is.md ... 45-case-study-gnn.md
    └── appendix-a-minimal-math.md ... appendix-g-python-stack.md
```

## License

This project is provided for educational purposes.
