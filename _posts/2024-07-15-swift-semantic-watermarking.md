---
layout: post
title: "SWIFT: Semantic Watermarking for Image Forgery Thwarting"
author: Gautier Evennou, Vivien Chappelier and Ewa Kijak
excerpt: "Watermark semantic edits so detectors can flag tampered images without hurting clean media."
paper_url: https://arxiv.org/abs/2407.18995
code_url: https://github.com/gautierevn/swift_watermarking
tags:
  - conference:IEEE WIFS 2024
  - watermarking
  - image-forensics
  - reliability
---

## TL;DR

- Embed a semantic watermark during editing so post-hoc detectors can localize tampering.
- Keep perceptual quality intact on pristine assets by injecting the signal only when an edit happens.
- Release an open pipeline (paper + code) to reproduce benchmarks and ablations.

## Why SWIFT?

Semantic alteration is now cheap, but most forensic tools struggle to tell genuine stories from fabricated ones. Rather than guessing after the fact, SWIFT (Semantic Watermarking for Image Forgery Thwarting) nudges the generator to stamp an editable image with an imperceptible yet verifiable signal. The idea is simple: if we cooperate with the editing network, downstream auditors can later check authenticity without needing the original asset.

## How it works

1. **Watermark guidance** - while editing an image, we optimize a frequency-aware watermark loss so the edit carries a semantic signature.
2. **Tamper localization** - a lightweight detector recovers the watermark and highlights which pixels were modified.
3. **Integrity switch** - if an image is untouched, we skip the watermark step, so there is no quality hit on clean content.

The repository includes training scripts, evaluation notebooks, and ablation studies on robustness against compression, resizing, and intentional removal attempts.

## What's next

We are exploring how SWIFT pairs with generative provenance systems, and whether similar signals can travel across video timelines. Feedback and pull requests are welcome - grab the code, run the benchmarks, and let me know where to improve.
