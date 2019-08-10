---
layout:     post
title:      Evaluation Metrics for Natural Language Generation
author:     Jexus
tags: 		NLP Machine_Learning Deep_Learning
subtitle:   BLEU 及 ROUGE 的簡單介紹
category:  note
---

![title](http://upload.art.ifeng.com/2018/0408/1523156237505.jpg)

在人們做一些文字生成的task時(machine translation、text summarization)，要如何評估所生成文句的優劣呢？如果用人眼去看，當然很準，但是做一次實驗往往需要生成上萬句，無法用人眼來看。再者，如果在機器訓練過程中要人來參與的話，不知道要train到。那怎麼辦呢？人們想出了一些用機器評估的好方法，也就是BLEU和ROUGE，這兩個方法都是在很多年前就已經提出的，而且多少會覺得有點不完善，但仍然是目前最常使用的方法。

先借用[這篇](https://stackoverflow.com/questions/38045290/text-summarization-evaluation-bleu-vs-rouge)講一下 BLEU 跟 ROUGE 的差異：

- BLEU 量的是 Precision (準確率): 機器產生的句子的n-gram是否是正確的(有出現在正確答案句子的n-gram裡面)。
- ROUGE 量的是 Recall (召回率): 正確答案句子的n-gram是否被包含在機器產生的句子的n-gram裡面。

剛好在法語中 rouge 是紅色的意思，bleu是藍色的意思，應該是故意這樣取名的。所以如果之後想發類似主題的paper也許可以考慮 vert(綠色) XD。


## ROUGE

先來接介紹[ROUGE(Recall-Oriented Understudy for Gisting Evaluation)](http://www.aclweb.org/anthology/W04-1013)，這篇是早在2004年提出的，作者是[Chin-Yew Lin](https://www.microsoft.com/en-us/research/people/cyl/)，是交大校友，超強。

ROUGE有很多種：ROUGE-N, ROUGE-L, ROUGE-W, ROUGE-S

### ROUGE-N

定義如下，非常直覺：
"**在正確答案文章中的句子中的n-gram數量**"分之"**這些n-gram有跟機器產生的句子的n-gram重疊的數量**"  

$$\frac{\sum _{S\in \left\{ Reference\right\} } \sum _{gram_n\in \left\{ S\right\} } Count_{match}(gram_n)}{\sum _{S\in \left\{ Reference\right\} } \sum _{gram_n\in \left\{ S\right\} } Count(gram_n)}$$

因此ROUGE-1, ROUGE-2, ROUGE-3 就分別是在觀察 unigram, bigram, trigram 囉。

### ROUGE-L

他看的是 longest common sequence 的字數，$LCS(Ref,Gen)$指的是答案的文章(Reference)跟生成的文章(Generation)所有共現的 sequence 之中最長的 sequence 的長度，算precision跟recall：

$$P_{lcs} = \frac{LCS(Ref,Gen)}{wordcount_{Gen}}$$

$$R_{lcs} = \frac{LCS(Ref,Gen)}{wordcount_{Ref}}$$

那麼共現的 sequence 是甚麼意思呢？注意在這裡作者提到 "**it does not require consecutive matches**"，也就是說，`"我去買了一雙好鞋"`跟`"我出門買了一雙漂亮的鞋子"`，共現的 sequence 是 `"我買了一雙鞋"`，不用連續也行。

最後算分用的是F-score:

$$ROUGE_L = F_{lcs} = \frac{(1+\beta^2)P_{lcs}R_{lcs}}{R_{lcs}+\beta^2 P_{lcs}}$$

看起來是precision跟recall都用上了，不過在這裡通常$\beta$會是一個大的值，所以還是以recall為重。

如果Ref的句子恰巧跟Gen的句子一模一樣，那麼ROUGE-L算出來是1，反之如果完全沒有共現的句子，ROUGE-L算出來會是0。

### ROUGE-W

從剛剛的例子會發現，因為共現的 sequence 不需要連續，因此**共現的sequence比較連續的句子**跟**比較不連續的句子**得到的分數都會是一樣的，例子如下：

$$X(Ref): [ \underline{A} \underline{B} \underline{C} \underline{D} E F G]$$

$$Y_1(Gen): [ \underline{A} \underline{B} \underline{C} \underline{D} H I J]$$

$$Y_2(Gen): [ \underline{A} H \underline{B} I \underline{C} J \underline{D}]$$

這樣$Y_1$跟$Y_2$所算出來的分數一樣，不過你應該會覺得，$Y_1$還是比$Y_2$略好一些。為了解決這個問題，就有了*ROUGE-W: Weighted Longest Common Subsequence*。

詳細的演算法是怎麼算的呢？首先，假如要比較的句子分別是$X(Ref), Y(Gen)$，長度分別為$m, n$，那麼我先造兩個 m x n 的矩陣，一個叫做 $W$，另一個叫 $C$，今天對於$W$之中的每一個元素$w_{i,j}$，若$X_i \neq Y_j$，則$w_{i,j}=0$；若$X_i = Y_j$，則$w_{i,j}=w_{i-1,j-1}+1$，所以$W$存的就是該處累加的共現的sequence的長度。

注意，在此處$W, C$在$i=0$或是$j=0$的地方，$w_{i,j}, c_{i, j}$都直接被設為0，而且不更新，只有$i, j$都大於$1$才更新。

那$C$呢？若$X_i \neq Y_j$，則$c_{i,j} = max(c_{i-1,j}, c_{i,j-1})$；若$X_i = Y_j$，則$c_{i,j}=c_{i-1,j-1}+f(w_{i-1,j-1}+1)-f(w_{i-1,j-1})$，其中$f$是一個滿足$f(x+y) > f(x) + f(y)$的函數，如$f(x) = \alpha x-\beta$($\alpha, \beta$都大於0)，或是直接$f(x)=x^\alpha$。照這個算法，最後求出的$WLCS(X,Y)=c_{m,n}$。

最後一樣要算precision跟recall，$f^{-1}$是f的反函數：

$$P_{wlcs} = f^{-1}(\frac{WLCS(X,Y)}{f(m)})$$

$$R_{wlcs} = f^{-1}(\frac{WLCS(X,Y)}{f(n)})$$

最後算分用的是F-score:

$$ROUGE_L = F_{wlcs} = \frac{(1+\beta^2)P_{wlcs}R_{wlcs}}{R_{wlcs}+\beta^2 P_{wlcs}}$$

一樣$\beta$會是一個大的值，所以還是以recall為重。

## ROUGE-S
ROUGE-S跟ROUGE-N還蠻像的，但很無腦的把n-gram換成skip-n-gram，例如skip-bigram的話，就是把句子中$C ^m _2$種組合都拿去當成他的bi-gram來用。

$$P_{skip2} = \frac{SKIP2(X,Y)}{C(m,2)}$$

$$R_{skip2} = \frac{SKIP2(X,Y)}{C(n,2)}$$

$$ROUGE_S = F_{skip2} = \frac{(1+\beta^2)P_{skip2}R_{skip2}}{R_{skip2}+\beta^2 P_{skip2}}$$

當然，這樣的算法會把距離很遠的假bi-gram也當成是真的，我們可以下一個constrain$d_{skip}$使當距離大於$d_{skip}$就不算數。

## ROUGE-SU: ROUGE-S的加強版
如果兩個序列剛好是反序，那ROUGE-S算出來剛好就是0，因為每個skip-bigram都是反的，所以我們可以加入uni-gram的分數進去，變成ROUGE-SU，就比較有分辨性。

待續...
