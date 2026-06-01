# Profession Concept Overlap

This folder contains a Jupyter notebook for testing whether a model's SAE trait-related features activate differently across gendered profession prompts in English and Traditional Chinese.

The project is inspired by the `SAE_fairness` paper workflow, but it uses a narrower fixed-feature design:

1. Discover candidate trait features from competence and leadership prompts.
2. Filter those features using Neuronpedia descriptions.
3. Run fixed-feature activation inference on matched English and Traditional Chinese profession prompts.
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

The engineer leads the team.
The male engineer leads the team.
The female engineer leads the team.

這位工程師非常有能力。
這位男性工程師非常有能力。
這位女性工程師非常有能力。
```

The main metric is `attribute_activation`, which measures SAE feature activation on the explicit trait words in the prompt. For competence, this includes words such as `competent`, `skilled`, `qualified`, `expert`, `experienced`, and `capable`. For leadership, this includes words such as `leader`, `leadership`, `manages`, `supervises`, `directs`, `authority`, and `team`.

## Notebook Flow

The notebook is organized as:

1. Setup: model, SAE source, cache paths, API settings.
2. Prompts: trait discovery prompts and matched profession prompts.
3. Token helpers: content, role, gender, and attribute token matching.
4. Discover trait features: Neuronpedia `search-all` over competence and leadership prompts.
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
```

## Notes

The gender disparity plots do not use group averages. They compare matched male and female prompt pairs, keep only pairs where both activations are non-zero, and plot the largest absolute male-female gap per feature.

The Neuronpedia activation endpoint currently limits requests to 1000 per 60 minutes. The full bilingual run is close to that limit, especially if you have already run activation cells recently. If the notebook hits HTTP 429, it saves partial outputs and stops. Wait for the window to reset, then rerun the activation cell; cached activation responses will be reused. To reduce requests, temporarily set `TARGET_LANGUAGES = ["traditional_chinese"]` or `TARGET_LANGUAGES = ["english"]` before rebuilding `TARGET_PROMPTS`.

The current design tests whether gender context modulates explicit trait framing. The same fixed feature sets are tested against English and Traditional Chinese prompts so the language comparison does not change the feature pool. It does not test whether bare profession nouns implicitly activate competence or leadership features.
