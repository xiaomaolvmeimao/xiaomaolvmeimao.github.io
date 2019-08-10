---
layout:     post
title:      Ordered Neurons - Integrating Tree Structures into Recurrent Neural Networks​
author:     Jexus
tags: 		NLP deep_learning slide
subtitle:   ICLR 2019 (Best Paper Award) paper - slide
category:  slideshare
---

## Ordered Neurons: Integrating Tree Structures into Recurrent Neural Networks​

[Paper Link](https://arxiv.org/abs/1810.09536)

### TL;DR

利用嚴格遞增及嚴格遞減的條件去限制 LSTM 的 forget gate 及 input gate，讓 model 表現變好，並且在 training 完之後可以根據 forget gate 遺忘程度的多寡來找出藏在句子中的 Tree Structure，做到Unsupervised Syntactic Parsing。 

此 paper 於 ICLR 2019 獲 Best Paper Award。

### Slide:

> Please wait a minute for the embedded frame to be displayed. Reading it on a computer screen is better.




<iframe src="https://onedrive.live.com/embed?cid=255C96F3631B0025&amp;resid=255C96F3631B0025%21416&amp;authkey=AOz8B3tPCtQAeh4&amp;em=2&amp;wdAr=1.7777777777777777" width="962px" height="565px" frameborder="0">這是 <a target="_blank" href="https://office.com/webapps">Office</a> 提供的內嵌 <a target="_blank" href="https://office.com">Microsoft Office</a> 簡報。</iframe>

關於這篇提出的嚴格遞增及嚴格遞減的 gate 條件，是否真的能讓 model 表現變好，有討論於此：  
*「如何评价ICLR 2019 best paper: Ordered Neurons ?」*
https://www.zhihu.com/question/323190069

![](https://i.imgur.com/ukqfwtQ.png)

很可能是前人實驗 report 了較低的分數造成。