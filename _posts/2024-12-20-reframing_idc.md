---
layout: post
title: "Reframing Image Difference Captioning with BLIP2IDC and Synthetic Augmentation"
author: Gautier Evennou, Antoine Chaffin, Vivien Chappelier, and Ewa Kijak
excerpt: "Using VLM and synthetic data to improve Image Difference Captioning"
paper_url: https://arxiv.org/abs/2412.15939
code_url: https://github.com/gautierevn/BLIP2IDC
thumbnail: /assets/images/thumbnails/reframing_idc_thumbnail.png
tags:
  - conference:IEEE WACV 2025
  - presentation:Oral
---

Oral Paper Round 1 Acceptance (for those who track such things)

I am a bit late to the party, but I wanted to share the first publication of my PhD.

I work on semantic alteration in multimedia content and we wanted to compare two images: one original and one edited. Then came InstructPix2Pix, a milestone in image editing models. We thought "this model generates new versions of an image; it would be nice to use it for synthetic data because that space is underexplored in computer vision." Since then synthetic data has exploded, but at that time it was fairly new.

![InstructPix2Pix — Learning to Follow Image Editing Instructions (Brooks et al., 2022)]({{ "/assets/images/ip2p_mainframe.jpeg" | relative_url }})
*Figure 1. InstructPix2Pix — Learning to Follow Image Editing Instructions (Brooks et al., 2022).*


With this fresh idea, we started to think about how to compare images. The streamlined approach was to take two images independently, extract their features, merge them, and let a decoder output a difference caption. That was standard practice in IDC and change captioning. We picked BLIP2, a state-of-the-art image captioning model trained with synthetic data to generate better captions.

First hurdle: GPU access. At the beginning I had a single 4090, so I needed an efficient way to fine-tune BLIP2 (2.7B). Enter LoRA, which basically saved the paper. LoRA freezes the base model and swaps each linear layer with two low-rank matrices. In practice we use ranks 8, 16, or 32 - no more. You end up updating only a tiny fraction of the parameters, saving the adapters, and reloading them at inference. We fine-tune the transformer layers - the q, k, and v projections.

Once we could fine-tune, the classic parallel feature extraction pipeline underperformed. It was below state-of-the-art. Our intuition: this is the curse of dimensionality. By projecting features to high dimensions, representations drift apart. We wanted to bypass that. How do humans compare images? We place them side by side. So we tried the same: we concatenated the two images vertically and let BLIP2 process a joint 256x256 input. That joint encoding is the key - we do not want separate exhaustive representations; we want to represent differences directly.

With BLIP2 and joint encoding we reached state-of-the-art in about 20 minutes of LoRA fine-tuning. Not bad. It worked partly because most IDC papers focused on elaborate modules instead of leveraging large pretrained VLMs. We tried to nudge the field toward practical setups: pretrained models provide visual world knowledge, we add efficient finetuning plus synthetic augmentation to overcome data scarcity.

Synthetic augmentation was still new on the vision side. InstructPix2Pix generates text variations with GPT-3 and edited images from prompts, giving a controllable generator for data creation. We explored how far synthetic data alone can take BLIP2 for IDC and found it can reach SOTA performance.

Benchmarks, however, are flawed. Some methods barely outperform a constant caption (the mean sentence of a dataset). Many NLP metrics like BLEU4 or CIDEr need several references to be meaningful, yet most IDC datasets provide fewer than five. We tried to mitigate this by prompting Llama 2 to generate reference variations. It is not perfect, but it aligns better with what those metrics expect.

We also looked at attention maps to explain where the model focuses when producing a difference caption. That helps to justify the generated sentence.

IDC benchmarks are close to saturation and the task itself is constrained - it assumes access to both original and edited images. Real deployments might not have that luxury. If you care about that scenario, check my follow-up works such as SWIFT (Semantic Watermarking for Image Forgery Thwarting).

![Difference captioning attention examples](WACV%202025%20284383abbeb4803a8087e7ae44c3450e/attention_examples.jpeg)
