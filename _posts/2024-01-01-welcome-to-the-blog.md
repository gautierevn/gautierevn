---
layout: post
title: "WACV 2025"
author: Gautier Evennou
excerpt: "Reframing Image Difference Captioning with BLIP2IDC and Synthetic Augmentation."
---

# WACV 2025

**Reframing Image Difference Captioning with BLIP2IDC and Synthetic Augmentation** [https://arxiv.org/abs/2412.15939](https://arxiv.org/abs/2412.15939)

Oral Paper Round 1 Acceptance (for those who care about this)

Ok, a bit late to the party but i wanted to share the first work of my thesis.

I work on semantic alteration in multimedia content (whatever this is), and we wanted to see if we could compare two images, one original and one edited. Then came InstructPix2Pix, a milestone in image editing models ! We thought “Hey, we got this cool model to generate new version of an image, it would be nice to use it for synthetic data as it is underexplored in computer vision”. Obviously, since then a lot of work has been made on synthetic data but at this time it was pretty new.

![InstructPix2Pix, Learning to Follow Image Editing Instructions, Tim Brooks et. al 2022]({{ "/assets/images/ip2p_mainframe.jpeg" | relative_url }})

InstructPix2Pix, Learning to Follow Image Editing Instructions, Tim Brooks et. al 2022

[IP2P image, showcase variation in images]

With this fresh idea in my mind, we started to think about how to compare images. The most streamlined way of doing this is to take two images independently, extract their respective features and then somehow merge them so that a decoder output a “difference caption”. We saw that this was rather standard in IDC and Change Captioning. Then, we picked BLIP2, state-of-the-art image captioning model that was trained with synthetic data to generate better caption.

First hurdle, GPU access. At the beginning i only had access to one 4090 so i had to find a way to finetune BLIP2(2.7B) in an efficient manner. Then LoRA enters the chat and basically singlehandledly saves this paper. For those unfamiliar with LoRA, it stands for LowRankAdaption. It consists in freezing the whole model BLIP2, then replacing each linear layer with dim $k*d$ by two matrices A and B of dim $k*r | r*d$. In practice, we use a rank of 8,16, or 32 but no more. This accounts for a very efficient finetuning, as only 0.01% of the parameters of the original model are updated. Then only the LoRA matrices are saved and can be loaded into BLIP2 at inference. We finetune linear layer from transformers layers a.k.a the q,k,v matrices.

Now that we can finetune, using the classic pipeline of parallel feature extraction was performing pretty bad. Subpar to state-of-the-art method. Hum, strange right ? Our intuition on this is a classic lost in translation mechanism. By projecting features to a high dimensional space, they are apart from each other intrinsically (see the curse of dimensionality). Then we thought how to bypass this problem. How would you do it ? An intuitive way of solving this problem is to use anthropomorphism, “how would human compare two images” ? I guess it is no surprise that a sound human would put them side-by-side to limit noise due to unrelated information in his environment. Ok, let’s try it right ? Side-by-side it basically concatenation in the image space, so our BLIP2 will now take a $256²$ image made of two $128,256$ images. (vertical concatenation). And then we go through BLIP2 flow of image extraction, QFormer and text decoder. This idea of joint encoding does makes sense as we do not want to represent images and compare their representation as it requires exhaustive representation, we rather want to straightforwardly represent their differences as it is our main objective.

Ok cool, basically just by using BLIP2 and this joint encoding mechanism we got state-of-the-art in 20 minutes using LoRA ! Not so bad right ? Well it was only possible because in the IDC field most people were focused on designing super smart modules with convoluted math ~~to ensure acceptance at prestigious conference~~. We did not optimize for that. More importantly, we tried to nudge the field to be more practically oriented, like leveraging heavily pretrained VLMs instead of doing another training from scratch to earn 1% of performance. By that we mean solving two main issues of the IDC task: *knowledge of the visual world* and *data scarcity*. VLMs learned in the hard way the visual world, understanding relationships and nature of the objects in a scenery. But now, to adapt them to IDC, which biggest dataset comprises less than 3000 images, it is another story. Efficient Finetuning helps; LoRA tends to learn really fast and with few data but more quality data is the easiest path to success. That’s why we advocate for synthetic augmentation.

Data augmentation is well-know, i don’t think i need to introduce it here. Synthetic augmentation is more recent in the computer vision field; we leverage recent generative models as a way to produce quality data most aligned with our objective, hopefully without compromising with generalization to real world data. This has been used for years in the Natural Language Processing(NLP) field, which deals with text modality. But for image it is really the new generative paradigm, fueled by the release of StableDiffusion in 2022, which unlocked this approach. InstructPix2Pix generates text data using GPT3 and new versions of an image using those prompts. By doing so they train a new model, IP2P which is able to edit existing images. This work paved the way to many more, mainly led by the chinese industry (bytedance, alibaba, tencent). So now we got a perfect model to generate data to finetune our BLIP2 on, isn’t it great ?

We conducted many experiments, namely to see if synthetic data alone is enough to achieve good performances and reach a pretty cool conclusion that yes, synthetic data are enough to adapt BLIP2 to IDC and reach SOTA performances.

Here are some results to convince some of you.

Now that we have a super cool model and a cool way of gathering data, is there anything more to say ? In fact yes. But it has more to do with academic benchmarks and how flawed they are. I wont speak about goodhart law or criticize metrics. What i will say is that a sentence, trained to be the mean one using a train set of any dataset of IDC, is better than some methods published at prestigious conferences. Meaning that they basically do not output anything more significant than the most likely sentence that has no accept to input images. So yeah, there is that 😕 Moreover, NLP metrics such as BLEU4 or CIDEr need at least 5 references (ground-truths) to be meaningful. Most IDC datasets does not provide as much references as needed. We try to bypass this issue and make a more robust evaluation by prompting LLAMA 2 to generate variations of the reference. I am not a big fan to “put your problem in an LLM and paper”. Yeah it is clunky, yeah it introduces bias but it does make more sense and is more aligned with the spirit of the metrics so please forgive us. So we hope our results are more robust to.

Now, one more thing: here are some cool visualizations of attention maps from our model when it performs comparison between images. We basically see where it looks to take a decision and generate the difference caption. This could be very useful for explaining the difference caption, like telling “here is the difference, and here is the description of this difference”.

I wont dive into all the details of the paper, the goal of this blogpost is more to give you insights into my thought process and in the paper intuitions.

Having said that, i do not feel there is much to do in this task of IDC. The VLMs performance is skyrocketing this recent years and i expect them to perform better with time. Then, these saturated and flawed benchmarks we got wont be able to correctly assess significant improvements.

Also, this task is stringent by design for real-world applications, as it requires access to the edited image AND the original one. Would be cool to solve the same issue without the need for the original image right ? If you think so, have a look to my subsequent works, SWIFT : Semantic Watermarking for Image Forgery Thwarting and Fast, Secure an blabla. Those works, while being on different tasks than IDC, try to address the same issue.

![Difference captioning attention examples](WACV%202025%20284383abbeb4803a8087e7ae44c3450e/attention_examples.jpeg)
