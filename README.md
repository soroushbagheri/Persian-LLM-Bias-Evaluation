# Evaluating Social Bias in LLaMA and PersianLLaMA: A Cross-Lingual Analysis Using StereoSet

> **Master's Thesis** | Stockholm University | NLP & Machine Learning

[![Python](https://img.shields.io/badge/Python-3.10+-blue.svg)](https://www.python.org/)
[![HuggingFace](https://img.shields.io/badge/HuggingFace-Models-orange.svg)](https://huggingface.co/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

---

## 📖 Overview

This repository contains the full codebase, datasets, and analysis notebooks for the Master's thesis:

**"Evaluating Social Bias in Large Language Models: A Cross-Lingual Study of LLaMA-2 and PersianLLaMA Using StereoSet"**

The thesis investigates whether **social biases** present in the English LLaMA-2 model are amplified, attenuated, or altered when the model is adapted to the Persian language (PersianLLaMA). Four bias dimensions are examined — **gender, race, religion, and profession** — using a translated and annotated version of the StereoSet benchmark.

---

## 🎯 Research Questions

1. Do LLaMA-2 and PersianLLaMA exhibit measurable social biases across gender, race, religion, and profession categories?
2. How do bias magnitudes compare between the English and Persian versions of the same model?
3. Can tokenizer fragmentation explain cross-lingual differences in bias scores?
4. Which bias metric (PLL, CB, APX) is most sensitive and reliable for this evaluation?

---

## 🗃️ Dataset

### StereoSet (Adapted for Persian)

The benchmark is built on [StereoSet](https://stereoset.mit.edu/), a widely-used dataset for measuring stereotypical bias in LLMs. The original English sentences were **translated into Persian** and **manually verified** for semantic fidelity.

| Property | Value |
|---|---|
| Total instances | 720 |
| Bias categories | Gender (180), Race (180), Religion (180), Profession (180) |
| Label types | Stereotype, Anti-stereotype, Unrelated |
| Languages | English & Persian (parallel) |

Each entry contains:
- Context sentence with a `[BLANK]` token
- Three candidate sentences: stereotype, anti-stereotype, unrelated
- Parallel Persian translation
- Persian target word

---

## 🤖 Models

| Model | Description | Base |
|---|---|---|
| **LLaMA-2** | Meta's English instruction-tuned LLM | `meta-llama/Llama-2-7b-hf` |
| **PersianLLaMA** | Persian-adapted LLaMA-2 via continual pretraining | `persian-llm/PersianLLaMA` |

Both models were evaluated in **zero-shot** mode using three scoring metrics.

---

## 📐 Evaluation Metrics

### 1. Pseudo-Log-Likelihood (PLL)
Computes the log-likelihood of each candidate sentence using masked token scoring. Higher PLL → the model assigns higher probability to that sentence.

$$\text{PLL}(s) = \sum_{i=1}^{n} \log P(w_i \mid w_{\setminus i})$$

- **BiasScore (PLL-based)**: `PLL(stereotype) - PLL(anti-stereotype)`. A positive score indicates the model prefers the stereotypical sentence.

### 2. Categorical Bias (CB)
Aggregates PLL scores across all three sentence types (stereotype, anti-stereotype, unrelated) per context group to compute a normalized bias index.

$$\text{CB} = \frac{\sum \text{PLL}_{\text{stereotype}}}{\sum \text{PLL}_{\text{stereotype}} + \sum \text{PLL}_{\text{anti-stereotype}} + \sum \text{PLL}_{\text{unrelated}}}$$

### 3. Approximate Next-Token Probability (APX)
A faster approximation to PLL using next-token prediction probabilities rather than full masked scoring.



### 4. Tokenizer Fragmentation Features
Additional analysis on how tokenizer behavior differs across English and Persian, measuring:
- **Token count per sentence**
- **Fragmentation ratio** (subwords per word)
- Correlation between fragmentation and bias scores

---

## 📊 Key Results

### Overall Bias Comparison (PLL-based BiasScore)

| Model | Gender | Race | Religion | Profession |
|---|---|---|---|---|
| **LLaMA-2 (EN)** | +2.1 | +3.4 | +1.8 | +2.7 |
| **PersianLLaMA (FA)** | +4.2 | +2.9 | +6.1 | +3.3 |

> Positive values indicate preference for stereotypical sentences. PersianLLaMA shows **higher religious and gender bias** compared to the English model.

### Categorical Bias (CB) Scores by Bias Type

| Bias Type | LLaMA CB (EN) | PersianLLaMA CB (FA) |
|---|---|---|
| **Gender** | 22.08 | 15.26 |
| **Race** | 18.41 | 25.72 |
| **Religion** | 26.79 | 28.50 |
| **Profession** | 22.27 | 22.61 |

### APX Scores

The APX metric confirmed PLL-based trends. PersianLLaMA exhibited consistently higher APX bias scores for **race** and **religion** categories, suggesting adaptation to Persian training data introduced or amplified certain cultural stereotypes.

### Tokenizer Analysis

- PersianLLaMA applies heavier subword fragmentation to Persian text, especially for named entities and religious terms.
- Higher fragmentation was **positively correlated** with higher bias scores in the religious and racial categories.
- Fragmentation ratio for Persian: mean **3.2 subwords/word** vs. English **1.8 subwords/word**.

---

## 📁 Repository Structure

```
Persian-LLM-Bias-Evaluation/
│
├── README.md
│
├── notebooks/
│   ├── 01_PLL_Analysis_LLaMA.ipynb            # PLL scoring for LLaMA-2 (English)
│   ├── 02_PLL_Analysis_PersianLLaMA.ipynb     # PLL scoring for PersianLLaMA (Persian)
│   ├── 03_CB_Analysis_LLaMA.ipynb             # Categorical Bias for LLaMA-2
│   ├── 04_CB_Analysis_PersianLLaMA.ipynb      # Categorical Bias for PersianLLaMA
│   ├── 05_APX_Analysis_LLaMA.ipynb            # APX metric for LLaMA-2
│   ├── 06_APX_Analysis_PersianLLaMA.ipynb     # APX metric for PersianLLaMA
│   └── 07_FinalResults_FullAnalysis.ipynb     # Combined analysis, plots, conclusions
│
├── data/
│   ├── NewStreoSet_Dataset.xlsx               # Annotated StereoSet (EN + FA, raw)
│   ├── stereoset_LLama.csv                    # PLL results for LLaMA-2
│   ├── stereoset_persianLLama.csv             # PLL results for PersianLLaMA
│   ├── stereoset_LLama_ByBiasType.csv         # LLaMA-2 results grouped by bias type
│   ├── stereoset_persianLLama_ByBiasType.csv  # PersianLLaMA results grouped by bias type
│   ├── APX_LLaMA.csv                          # APX scores for LLaMA-2
│   ├── APX_persianLLaMA.csv                   # APX scores for PersianLLaMA
│   └── stereoset_with_tokenizer_features.csv  # Tokenizer feature analysis
│
└── requirements.txt
```

---

## ⚙️ Setup & Installation

### Prerequisites
- Python 3.10+
- GPU (recommended: NVIDIA A100 / T4 via Google Colab)
- HuggingFace account with access to LLaMA-2 weights

### Install Dependencies

```bash
git clone https://github.com/soroushbagheri/Persian-LLM-Bias-Evaluation.git
cd Persian-LLM-Bias-Evaluation
pip install -r requirements.txt
```

### HuggingFace Login

```python
from huggingface_hub import login
login(token="your_hf_token")
```

---

## 🚀 Running the Analysis

All analysis is structured as Jupyter Notebooks. You can run them sequentially:

```
notebooks/01 → 02 → 03 → 04 → 05 → 06 → 07
```

Or open the **Full Analysis notebook** directly:

```
notebooks/07_FinalResults_FullAnalysis.ipynb
```

Alternatively, open in **Google Colab**:

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1g5b5Mptu12Of0lzMf4x_bp1dbSclwoas)

---

## 📦 Data Files Description

| File | Description |
|---|---|
| `NewStreoSet_Dataset.xlsx` | Original annotated dataset with English and Persian sentence pairs, targets, and gold labels |
| `stereoset_LLama.csv` | PLL scores per sentence for LLaMA-2, including BiasScore and CB |
| `stereoset_persianLLama.csv` | Same as above for PersianLLaMA |
| `stereoset_LLama_ByBiasType.csv` | LLaMA-2 results with CB values aggregated by bias type (gender/race/religion/profession) |
| `stereoset_persianLLama_ByBiasType.csv` | PersianLLaMA results with CB values by bias type |
| `APX_LLaMA.csv` | APX (next-token approximation) scores for LLaMA-2 |
| `APX_persianLLaMA.csv` | APX scores for PersianLLaMA |
| `stereoset_with_tokenizer_features.csv` | Extended dataset with tokenizer features (token counts, fragmentation ratio) for both models |

---

## 📚 Related Work

- Nadeem et al. (2021) — [StereoSet: Measuring Stereotypical Bias in LLMs](https://aclanthology.org/2021.acl-long.416/)
- Touvron et al. (2023) — [LLaMA 2: Open Foundation and Fine-Tuned Chat Models](https://arxiv.org/abs/2307.09288)
- Saleh et al. (2023) — PersianLLaMA: Persian Language Adaptation of LLaMA
- Nangia et al. (2020) — [CrowS-Pairs: A Challenge Dataset for Measuring Social Biases](https://aclanthology.org/2020.emnlp-main.154/)
- Liang et al. (2022) — [Holistic Evaluation of Language Models (HELM)](https://arxiv.org/abs/2211.09110)

---

## 🧠 Author

**Soroush Bagheri**  
MSc Computer Science — Stockholm University  
Research focus: NLP, Bias in LLMs, Multilingual AI  
📧 [GitHub](https://github.com/soroushbagheri)

---

## 📄 License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

---

## 🙏 Acknowledgements

This thesis was conducted at Stockholm University. Special thanks to the supervisors and the NLP research group for guidance throughout the project. The StereoSet dataset is used under academic research terms.
