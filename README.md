# Honoring the Rules? A Targeted Syntactic Evaluation of Honorific Mismatches in Korean LLMs

This repository contains the dataset and evaluation code for the paper **"Honoring the Rules? A Targeted Syntactic Evaluation of Honorific Mismatches in Korean LLMs"**.

---

## Overview

This study investigates whether Korean large language models (LLMs) exhibit human-like sensitivity to honorific mismatches, using Targeted Syntactic Evaluation. We evaluate five autoregressive Korean LLMs across two experiments — a baseline experiment on case marking and a main experiment on honorific agreement — using two metrics: **Mean Surprisal** and **SLOR (Syntactic Log-Odds Ratio)**.

---

## Repository Structure

```
.
├── data/
│   ├── baseline/               # Baseline dataset (case marking)
│   │   └── case_marking.csv    # NOM+ACC, ACC+NOM, NOM+NOM, ACC+ACC conditions (256 sentences)
│   └── main/                   # Main dataset (honorific agreement)
│       └── honorific.csv       # HON+HON, HON+PLAIN, PLAIN+PLAIN, PLAIN+HON (1,536 sentences)
├── notebooks/
│   ├── Unigram.ipynb           # Unigram probability estimation from Korean Wikipedia
│   └── honorific_experiment.ipynb  # Main experiment (surprisal & SLOR computation)
├── results/
│   ├── baseline_results.csv    # Baseline experiment results
│   └── main_results.csv        # Main experiment results
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

All experiments are implemented as Google Colab notebooks. To reproduce the results:

1. Open the notebooks in the `notebooks/` directory in Google Colab.
2. Mount your Google Drive when prompted.
3. Run `Unigram.ipynb` first to estimate unigram probabilities from the Korean Wikipedia corpus.
4. Run `honorific_experiment.ipynb` to compute Mean Surprisal and SLOR across all models and conditions.

---

## Results Summary

### Baseline (Case Marking)

All five models successfully distinguished the grammatical NOM+ACC condition from all three ungrammatical conditions under both metrics (p < .001).

### Main (Honorific Agreement)

| Comparison | SLOR | Mean Surprisal |
|---|---|---|
| HON+HON vs. mismatches | Native-like across all 5 models | Native-like for Kanana + all 3 Polyglot-Ko models |
| PLAIN+PLAIN vs. mismatches | Non-native-like for most models | Native-like for all 3 Polyglot-Ko models |
| HON+PLAIN vs. PLAIN+HON (within-mismatch) | Non-native-like across all 5 models* | Native-like for all 3 Polyglot-Ko models |

> *The reversal observed under SLOR for the within-mismatch comparison is attributed to the relative rarity of honorific verbal forms in the Korean Wikipedia corpus, which artificially inflates SLOR scores for sentences containing honorific verbs.


