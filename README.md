# Profession Concept Overlap

This folder contains a Jupyter notebook for testing whether a model's SAE competence-related features activate differently across gendered profession prompts.

The project is inspired by the `SAE_fairness` paper workflow, but it uses a narrower fixed-feature design:

1. Discover candidate competence features from competence prompts.
2. Filter those features using Neuronpedia descriptions.
3. Run fixed-feature activation inference on matched profession prompts.
4. Compare activation patterns across control, male, and female prompt variants.

## Main Notebook

`doctor_nurse_internal_concepts.ipynb`

Despite the older filename, the notebook now compares four professions:

- `doctor`
- `engineer`
- `pilot`
- `teacher`

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
2. Prompts: competence discovery prompts and matched profession prompts.
3. Token helpers: content, role, gender, and attribute token matching.
4. Discover competence features: Neuronpedia `search-all` over competence prompts.
5. Review descriptions: fetch Neuronpedia feature descriptions.
6. Filter features: keep category-relevant features and block noisy categories.
7. Feature activations: run fixed-feature activation inference on target prompts.
8. Group summary: compare mean activations by language, trait, profession, and gender group.
9. Feature summary: long-format feature-level activation table.
10. Trait/profession tables: compact feature tables per trait and profession.
11. Feature difference plots: largest control/male/female spread per language, trait, and profession.
12. Gender disparity plots: largest non-zero matched male-female gaps per language, trait, and profession.
13. Group plot: language-by-trait-by-profession-by-gender activation summary.

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
TARGET_LANGUAGES = ["english", "traditional_chinese"]
SEARCH_MAX_RETRIES = 5
SEARCH_RETRY_SECONDS = 20
```

## Notes

The gender disparity plots do not use group averages. They compare matched male and female prompt pairs, keep only pairs where both activations are non-zero, and plot the largest absolute male-female gap per feature.

The Neuronpedia activation endpoint currently limits requests to 1000 per 60 minutes. The full bilingual four-profession run is larger than the previous doctor/engineer run. Cached activation responses will be reused on reruns, but new uncached activations may approach the rate limit.

Uncached Neuronpedia `search-all` requests can occasionally time out. The notebook retries those requests before failing; already cached search responses are loaded locally and do not call Neuronpedia again.

The current design tests whether gender context modulates explicit competence framing across English and Traditional Chinese prompts. It does not test whether bare profession nouns implicitly activate competence features.
