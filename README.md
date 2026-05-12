# Honoring the Rules? A Targeted Syntactic Evaluation of Honorific Mismatches in Korean LLMs

This repository contains the dataset and evaluation code for the paper **"Honoring the Rules? A Targeted Syntactic Evaluation of Honorific Mismatches in Korean LLMs"**.

---

## Overview

This study investigates whether Korean large language models (LLMs) exhibit human-like sensitivity to honorific mismatches, using Targeted Syntactic Evaluation. We evaluate five autoregressive Korean LLMs across two experiments — a baseline experiment on case marking and a main experiment on honorific agreement — using two metrics: **Mean Surprisal** and **SLOR (Syntactic Log-Odds Ratio)**.

---

## Repository Structure

```
.
├── Dataset/
│   ├── case_marking.csv    # NOM+ACC, ACC+NOM, NOM+NOM, ACC+ACC conditions (256 sentences)
│   └── honorific.csv       # HON+HON, HON+PLAIN, PLAIN+PLAIN, PLAIN+HON (1,536 sentences)
├── generate_dataset.ipynb  # Dataset generation
├── Unigram.ipynb           # Unigram probability estimation from Korean Wikipedia
├── honorific_experiment.ipynb  # Main experiment (surprisal & SLOR computation)
└── README.md
```

---

## Dataset

### Baseline Experiment (Case Marking)

A total of **256 sentences** generated from combinations of 4 subjects (teacher, father, student, child), 4 verbs (eat, read, buy, look for), and 4 objects per verb, across 4 case conditions (4 subjects × 4 verbs × 4 objects × 4 conditions).

| Condition | Description |
|-----------|-------------|
| NOM + ACC | Nominative–Accusative (grammatical) |
| ACC + NOM | Accusative–Nominative (ungrammatical) |
| NOM + NOM | Nominative–Nominative (ungrammatical) |
| ACC + ACC | Accusative–Accusative (ungrammatical) |

### Main Experiment (Honorific Agreement)

A total of **1,536 sentences** generated from combinations of 8 honorific nouns, 8 plain nouns, and 6 verb pairs (plain/honorific form) across 4 sentence types (4 types × 8 × 8 × 6).

| Type | Example | Description |
|------|---------|-------------|
| HON + HON | sensayng-nim-i ai-lul chac-usi-ess-ta | Match (grammatical) |
| HON + PLAIN | sensayng-nim-i ai-lul chac-ass-ta | Mismatch |
| PLAIN + PLAIN | haksayng-i ai-lul chac-ass-ta | Match (grammatical) |
| PLAIN + HON | haksayng-i ai-lul chac-usi-ess-ta | Mismatch |

---

## Models

| Model Family | Model ID |
|---|---|
| EXAONE | `LGAI-EXAONE/EXAONE-4.0-1.2B` |
| Kanana | `kakaocorp/kanana-1.5-2.1b-base` |
| Polyglot-Ko | `EleutherAI/polyglot-ko-1.3b` |
| Polyglot-Ko | `EleutherAI/polyglot-ko-3.8b` |
| Polyglot-Ko | `EleutherAI/polyglot-ko-5.8b` |

---

## Evaluation Metrics

**Mean Surprisal**: The average surprisal across all tokens in a sentence. Lower values indicate that the model finds the sentence more expected, serving as a proxy for acceptability.

$$S = -\frac{1}{n} \sum_{i=1}^{n} \log_2 p(x_i | x_1, \ldots, x_{i-1})$$

**SLOR**: The Syntactic Log-Odds Ratio (Lau et al., 2017), which normalizes log probability by sentence length and unigram probability to control for confounds such as sentence length and lexical frequency. Higher values indicate greater acceptability.

$$\text{SLOR}(\xi) = \frac{\log p_m(\xi) - \log p_u(\xi)}{|\xi|}$$

> **Note**: Unigram probabilities are estimated from the Korean Wikipedia corpus (20231101.ko), tokenized using each model's tokenizer with Laplace smoothing (α=1.0).

---

## Setup & Usage

All experiments are implemented as Google Colab notebooks and require a GPU runtime (except Step 0).

### Step 0: Dataset Generation (`generate_dataset.ipynb`)

This notebook generates the stimulus sentences for both experiments and saves them as CSV files.

1. Open `generate_dataset.ipynb` in Google Colab (no GPU required).
2. Run all cells. The following files will be saved:
    - `./data/baseline/case_marking.csv` (256 sentences)
    - `./data/main/honorific.csv` (1,536 sentences)

### Step 1: Unigram Estimation (`Unigram.ipynb`)

This notebook estimates unigram probabilities from the Korean Wikipedia corpus (20231101.ko) for each model's tokenizer, which are required for SLOR computation.

1. Open `Unigram.ipynb` in Google Colab and set the runtime to GPU.
2. Set the `MODEL` variable to the model of your choice:
    ```python
    MODEL = "EleutherAI/polyglot-ko-1.3b"  # change as needed
    ```
3. Run all cells. The estimated unigram log-probabilities will be saved as a `.pkl` file to `./results/unigram/`.
4. Repeat for each model.

### Step 2: Main Experiment (`honorific_experiment.ipynb`)

This notebook generates the stimulus sentences, scores them using Mean Surprisal and SLOR, runs statistical tests, and produces violin plots.

1. Open `honorific_experiment.ipynb` in Google Colab and set the runtime to GPU.
2. Set `MODEL_NAME` and `PHENOMENON` in the Settings cell:
    ```python
    MODEL_NAME = "EleutherAI/polyglot-ko-5.8b"  # change as needed
    PHENOMENON = "honorific"  # "honorific" or "case_marking"
    ```
3. Run all cells. Results (CSV) will be saved to `./results/{PHENOMENON}/data/` and plots (PNG) to `./results/{PHENOMENON}/SLOR/` and `./results/{PHENOMENON}/Surprisal/`.


