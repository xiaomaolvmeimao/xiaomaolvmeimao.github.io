---
layout:     post
title:      Pay Less Attention with Lightweight and Dynamic Convolutions​
author:     Jexus
tags: 		NLP deep_learning slide
subtitle:   ICLR 2019 (Oral) paper - slide
category:  slideshare
---

## Pay Less Attention with Lightweight and Dynamic Convolutions​

[Paper Link](https://arxiv.org/abs/1901.10430)

### TL;DR

把 Transformer 架構中的 self-attention 部分全部換成 Convolution Layer，發現其實表現不差，且多加一些小 trick 就能贏過 self-attention-based Transformer。

此實驗結果並不是要說 Convolution Layer 很強，而是要說 Transformer 能表現這麼好，其架構中除了Attention 部分外的其他部分 (如：FFN blocks between the self-attention module) 可能才是扮演了重要的角色。

然而其實，這篇在處理 Encoder 跟 Decoder 之間的連接時，還是用了 Attention，沒有全部換掉，所以拿這篇說 Attention 過譽了是有點不公平，只能說拿來說 self-attention 過譽，不過但這部分也不方便用 Convolution Layer 取代就是了。

![](https://i.imgur.com/ZH44lw9.png)
https://openreview.net/forum?id=SkVhlh09tX

此作品是 [Felix Wu](https://sites.google.com/view/felixwu/home) (是 NTU CSIE 校友) 在 Facebook AI Research 實習的作品，code 已放在 [fairseq](https://github.com/pytorch/fairseq/blob/master/examples/pay_less_attention_paper/README.md)。

另有 [Talk Video on ICLR 2019](https://www.facebook.com/iclr.cc/videos/2183069775115828/)，從55分附近開始。

### Slide:

> Please wait a minute for the embedded frame to be displayed. Reading it on a computer screen is better.



<iframe src="https://onedrive.live.com/embed?cid=255C96F3631B0025&amp;resid=255C96F3631B0025%21414&amp;authkey=AOzCcnwnUdqGQqk&amp;em=2&amp;wdAr=1.7777777777777777" width="962px" height="565px" frameborder="0">這是 <a target="_blank" href="https://office.com/webapps">Office</a> 提供的內嵌 <a target="_blank" href="https://office.com">Microsoft Office</a> 簡報。</iframe>