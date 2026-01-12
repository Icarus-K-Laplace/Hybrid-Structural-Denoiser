# Hybrid-Structural-Denoiser
Hybrid-Structural-Denoiser: A two-stage framework for high-density impulse noise removal. It decouples robustness from geometry by first applying adaptive statistical filtering to remove outliers, then using structure tensors to guide directional refinement, restoring clean edges without deep learning.
# Hybrid-Structural-Denoiser 📐

### A Two-Stage Restoration Framework Combining Statistical Filtering and Structure Tensors

> **Developed independently by an undergraduate student.**
> This project demonstrates how classical geometric methods can effectively complement statistical filtering to solve high-density impulse noise problems, achieving state-of-the-art results without deep learning.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)

---

## 💡 The Problem & My Thinking Process

While deep learning dominates modern denoising, I noticed a fundamental gap in handling **impulse noise (salt-and-pepper)**:

1.  **Statistical filters (e.g., Median)** are robust but destroy fine edges, leaving "jagged" artifacts.
2.  **Geometric methods (e.g., Total Variation)** preserve edges but fail when noise density is high (>40%), as gradients become unreliable.

**My Hypothesis:**
> *Can we decouple robustness from structure preservation?*
> If we first use a robust statistical filter to get a "clean-enough" base, we can then compute reliable structure tensors to guide a second-stage refinement, fixing the artifacts of the first stage.

This repository implements this **Hybrid Two-Stage Strategy**.

---

## 🛠️ Algorithm Overview

### Stage 1: Robust Base Restoration
- **Goal**: Remove 99% of impulse noise outliers.
- **Method**: Adaptive statistical filtering. It dynamically expands the search window (3x3 $\to$ 7x7) to find valid pixels, ensuring robustness even at **60%+ noise density**.

### Stage 2: Directional Structural Refinement
- **Goal**: Fix jagged edges and restore geometric continuity.
- **Method**:
    1.  Compute the **Structure Tensor** ($S = \nabla I \cdot \nabla I^T$) on the Stage 1 output.
    2.  Analyze **Eigenvalues** ($\lambda_1, \lambda_2$) to detect strong edges and their orientation.
    3.  Apply **Directional Smoothing** *along* the edge tangent (not across it), effectively reconnecting broken structures.

---

## 📊 Performance

Tested on standard infrared samples (512x512):

| Noise Density | Method | PSNR (dB) | Visual Quality |
|:---:|:---:|:---:|:---:|
| **50%** | Standard Median | 28.5 | Blurry, jagged edges |
| **50%** | **Hybrid-Structural (Ours)** | **39.1** | **Sharp, continuous edges** |

*> Note: The PSNR gain is significant, but the visual improvement in edge continuity is the key contribution.*

---

## 🚀 Usage

### 1. Installation
Pure Python, minimal dependencies.
```bash
pip install numpy opencv-python
