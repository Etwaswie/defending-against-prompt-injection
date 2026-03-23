# 🛡️ Defending Against Prompt Injection with Defensive Tokens

[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.0+-red.svg)](https://pytorch.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## 📌 Overview

This repository contains an implementation and evaluation of **Defensive Tokens** — a method for protecting Large Language Models (LLMs) against **prompt injection attacks**, as proposed in the paper:

> *"Defending Against Prompt Injection With a Few DefensiveTokens"*  
> Sizhe Chen, Yizhu Wang, Nicholas Carlini, Chawin Sitawarin, David Wagner  
> AISec '25

The key idea is to insert **5 special trainable tokens** at the beginning of the prompt. Their embeddings are optimized to make the model ignore malicious instructions injected into external data, while preserving normal functionality on benign inputs.

## 🎯 Key Results

| Defense Method | Attack Success Rate (ASR) |
|----------------|--------------------------|
| No Defense | 79.3% |
| Reminder Defense | 79.3% |
| Sandwich Defense | 2.7% |
| **Defensive Tokens** | **0.0%** |

> ✅ Defensive Tokens **completely eliminate** prompt injection attacks!

### 📊 Utility Impact

Defensive Tokens have **negligible impact** on model quality for benign queries:
- Average response length: 45.2 (baseline) vs 44.8 (with defense)
- ROUGE-L score: 0.312 vs 0.308

## 🔬 Interpretability

PCA visualization shows that defensive token embeddings lie far from the vocabulary embedding space and have **anomalously large norms** (~100 vs ~1 for normal tokens), acting as "signal flags" that switch the model into a secure mode.

![PCA Visualization](images/pca_visualization.png)

## 🏗️ Architecture
[DT1][DT2][DT3][DT4][DT5] + [SYSTEM] + [USER] + [DATA] → LLM → Safe Response

- **DT1–DT5**: 5 trainable defensive tokens
- Model weights are **frozen** — only token embeddings are optimized
- Only ~10,000 trainable parameters (0.01% of model size)

## 🚀 Getting Started

### Prerequisites

```bash
pip install -r requirements.txt
```

Running the Experiment
The full experiment is contained in the Jupyter notebook:
```bash
jupyter notebook notebooks/defensive_tokens_experiment.ipynb
```

The notebook includes:

- Loading Qwen2.5-3B-Instruct model
- Self-labeling with Alpaca dataset
- Creating defensive dataset with prompt injections
- Training defensive tokens
- Evaluating ASR for 4 defense methods
- Visualizing embeddings for interpretability

## 🧪 Experimental Setup
- Model: Qwen2.5-3B-Instruct (1.7B parameters in 4-bit)
- Training Data: Cleaned Alpaca (2,000 samples)
- Evaluation Data: AlpacaFarm (208 samples with non-empty input)
- Attacks Tested: Ignore, Completion, Ignore+Completion
- Defenses Compared: None, Reminder, Sandwich, DefensiveTokens

## 💡 Key Findings
- Base model is highly vulnerable to prompt injection (ASR = 79.3%)
- Text-based defenses are unreliable — Reminder defense showed no effect
- Sandwich defense reduced ASR to 2.7% but not perfect
- Defensive Tokens achieve complete protection (ASR = 0.0%)
- No degradation in utility — responses on benign queries remain high-quality

## ⚠️ Limitations
- Does not protect against jailbreak attacks (where the user is malicious)
- Tokens must be trained separately for each model architecture
- Requires access to model gradients for training (done by model provider)

## 📚 References
- Original Paper
- StruQ: Defending Against Prompt Injection with Structured Queries
- Alpaca Dataset
- AlpacaFarm

## 🙏 Acknowledgements
- UC Berkeley researchers for the original DefensiveToken paper
- Alibaba Cloud for the Qwen2.5 model
- Hugging Face for transformers and datasets libraries
