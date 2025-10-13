---
layout: post
title: "SWIFT: Semantic Watermarking for Image Forgery Thwarting"
author: Gautier Evennou, Vivien Chappelier and Ewa Kijak
excerpt: "Watermark semantic edits so detectors can flag tampered images without hurting clean media."
paper_url: https://arxiv.org/abs/2407.18995
code_url: https://github.com/gautierevn/swift_watermarking
thumbnail: /assets/images/thumbnails/swift_thumbnail.png
tags:
  - conference:IEEE WIFS 2024
  - presentation:Oral
  - watermarking
  - image-forensics
  - reliability
---

## TL;DR

- Embed a semantic watermark so post-hoc detectors can assert authenticity.
- Keep perceptual quality intact on pristine assets by injecting the signal imperceptibly.
- Release an open pipeline (paper + code) to reproduce benchmarks and ablations.

## Why SWIFT?

Semantic alteration is now cheap, with image editing leading the charge. Most forensic tools struggle to tell genuine stories from fabricated ones, the best being only able to tell is a specific AI generator was used. Rather than guessing after the fact, SWIFT (Semantic Watermarking for Image Forgery Thwarting) embed the meaning of the image by projecting a caption into an imperceptible yet verifiable signal. The idea is simple: if we know what the image was using the caption, downstream auditors can later check authenticity without needing the original asset.

## How it works

1. **Caption** - a captioning model is responsible for providing a textual description of the image.
2. **Real Vector watermarking** - Usually, deep-learning based watermarking models embed bits, not real vectors. We chose this path as it enables us to carry more information.
3. **Confidence Metric** - at the receiving end, a confidence metric is computed to know is the decoded message is close from its perfect representation. This metric is heavily correlated with correctness and ensure the trustworthiness of the watermark.


## What's next

SWIFT was really fun to write as it introduced a new idea in the field of watermarking, using it as a communication channel. But the capacity is rather limited, up to 48 bits and then the interferences from the modulation become to much of a burden. So next work will focus on trying to bypass this modulation that is responsible for projecting bits into a real-valued vector. 
