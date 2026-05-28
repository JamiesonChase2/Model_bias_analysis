# Profession Concept Overlap

This folder contains a Jupyter notebook for testing whether a model's SAE expertise-related features activate differently across gendered profession prompts.

The project is inspired by the `SAE_fairness` paper workflow, but it uses a narrower fixed-feature design:

1. Discover candidate expertise features from competence prompts.
2. Filter those features using Neuronpedia descriptions.
3. Run fixed-feature activation inference on matched profession prompts.
4. Compare activation patterns across control, male, and female prompt variants.

## Main Notebook

`doctor_nurse_internal_concepts.ipynb`

Despite the older filename, the notebook now compares two professions:

- `doctor`
- `engineer`

Each profession has three prompt groups:

- `control`: no gender marker
- `male`: explicitly male
- `female`: explicitly female

Example matched prompts:

```text
The engineer is highly competent.
The male engineer is highly competent.
The female engineer is highly competent.
```

The main metric is `attribute_activation`, which measures SAE feature activation on explicit competence words such as `competent`, `skilled`, `qualified`, `expert`, `experienced`, and `capable`.

## Notebook Flow

The notebook is organized as:

1. Setup: model, SAE source, cache paths, API settings.
2. Prompts: expertise discovery prompts and matched profession prompts.
3. Token helpers: content, role, gender, and attribute token matching.
4. Discover expertise features: Neuronpedia `search-all` over expertise prompts.
5. Review descriptions: fetch Neuronpedia feature descriptions.
6. Filter features: keep competence-like features and block noisy categories.
7. Feature activations: run fixed-feature activation inference on target prompts.
8. Group summary: compare mean activations by profession and gender group.
9. Feature summary: long-format feature-level activation table.
10. Profession tables: compact feature tables per profession.
11. Feature difference plots: largest control/male/female spread per profession.
12. Gender disparity plots: largest non-zero matched male-female gaps per profession.
13. Group plot: profession-by-gender activation summary.

## Directory Contents

`data/`

Local Neuronpedia cache files. This includes search results, feature details, and activation responses. These files can become large and are ignored by git.

`outputs/`

Generated CSV summaries from notebook runs. These are ignored by git because they can be regenerated.

`doctor_nurse_internal_concepts.ipynb`

The main analysis notebook.

`README.md`

This file.

`requirements.txt`

Minimal Python dependencies for running the notebook.

## Requirements

Install dependencies:

```bash
python3 -m pip install -r requirements.txt
```

Set a Neuronpedia API key before running uncached cells:

```bash
export NEURONPEDIA_API_KEY="your_key_here"
```

The notebook defaults to:

```python
MODEL_ID = "gemma-2-2b"
SOURCE_SET = "gemmascope-res-16k"
```

## Notes

The gender disparity plot does not use group averages. It compares matched male and female prompt pairs, keeps only pairs where both activations are non-zero, and plots the largest absolute male-female gap per feature.

The current design tests whether gender context modulates explicit competence framing. It does not test whether bare profession nouns implicitly activate expertise features.
