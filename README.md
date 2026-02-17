# DA-DPO: Mitigating the Alignment Tax for Robust ASR

This repository contains the implementation of **Divergence-Aware Direct Preference Optimization (DA-DPO)**, a novel alignment objective designed to prevent performance degradation on the acoustic long-tail during the post-training of Speech Large Language Models (SpeechLLMs). 

> [!IMPORTANT]  
> This repository will be made publicly available upon paper acceptance. Currently under review.

## Purpose
While Direct Preference Optimization (DPO) is effective for global alignment, it often imposes an "alignment tax" that causes performance regressions on challenging subgroups such as non-native accents, elderly speakers, or noisy environments. 

**DA-DPO** addresses this by integrating subgroup divergence scores into the preference loss. This mechanism:
1. **Anchors** the model to prevent catastrophic forgetting on difficult edge cases.
2. **Steers** optimization to prioritize closing performance gaps on marginalized cohorts.

Our results demonstrate a reduction in subgroup bias by up to **48.5%** while maintaining or improving global Word Error Rate (WER).

---

## Main Experimental Setup

* **Backbone Model:** [Qwen2.5-Omni-7B](https://huggingface.co/Qwen/Qwen2.5-Omni-7B).
* **Adaptation Method:** Low-Rank Adaptation (LoRA) with $r=128$ and $\alpha=256$.
* **Training Stages:**
    * **SFT:** Supervised Fine-Tuning on ground-truth transcriptions.
    * **Preference Alignment:** 1 epoch of DA-DPO using the SFT checkpoint as the base policy.
* **Optimization Details:** 
    * $\beta = 0.1$ (DPO hyperparameter).
    * $\lambda = 1.0$ (DA-DPO penalty coefficient).
    * Minimum support threshold for subgroup discovery: $0.03$.

---

## Metadata Specification

For subgroup discovery and divergence analysis, we utilize the following metadata attributes categorized by demographic, speaking style, and signal parameters.

### 1. SpeechOcean
Focuses on non-native English speech and accent-level analysis.

| Category | Attributes |
| :--- | :--- |
| **Demographics** | `gender`, `age` |
| **Speaking Style** | `fluency`, `prosodic` |
| **Signal Parameters** | `total_silence`, `total_duration`, `n_pauses`, `n_words`, `speed_rate_word` |

### 2. Casual Conversations v2 (CCv2)
Focuses on diverse demographics and varying recording conditions.

| Category | Attributes |
| :--- | :--- |
| **Demographics** | `gender`, `age`, `native_language`, `secondary_languages`, `disabilities_hearing`, `disabilities_speech`, `disabilities_cognitive_learning_neuro_diversity`, `disabilities_use_of_assistive_technology`, `geo_country` |
| **Speaking Style** | `background_noise`, `voice_timbre` |
| **Signal Parameters** | `total_silence`, `total_duration`, `n_pauses`, `n_words`, `speed_rate_word` |
