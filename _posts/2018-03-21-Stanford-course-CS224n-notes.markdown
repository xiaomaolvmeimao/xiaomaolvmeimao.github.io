---
layout:     post
title:      Stanford - CS224n Natural Language Processing with Deep Learning - Course Notes
author:     Jexus
tags: 		NLP Machine_Learning Deep_Learning
subtitle:   CS224n (NLP with DL) 課程筆記
category:  note
---

>所有課程影片：https://www.youtube.com/playlist?list=PL3FW7Lu3i5Jsnh1rnUwq_TcylNr7EkRe

## Lecture 1 | Introduction to NLP and Deep Learning 
> [Slides](http://web.stanford.edu/class/cs224n/lectures/lecture1.pdf)  
> Suggested Readings:  
[Linear Algebra Review](http://web.stanford.edu/class/cs224n/readings/cs229-linalg.pdf)  
[Probability Review](http://web.stanford.edu/class/cs224n/readings/cs229-prob.pdf)  
[Convex Optimization Review](http://web.stanford.edu/class/cs224n/readings/cs229-cvxopt.pdf)  
[More Optimization (SGD) Review](http://cs231n.github.io/optimization-1/) 

第一堂課就是很top down方式的課程介紹，讓你大概了解整個NLP領域的Big picture。  
NLP著重在Syntactic analysis和Sementic Interpretation的部分。    
人類的語言是很奇怪的東西，他是discrete/symbolic/categorical的signal system。    
但科學家相信語言在人腦中encoding時是continuous pattern of activation，  
而語言傳播時也是透過continuous的信號，語言的sparsity對機器學習來說是個問題。    
2014年提出的word2vec也許是這個問題的解法，讓語言也能用continuous的方式在deep neural network中做optmization，下一堂課就會介紹word2vec。Deep learning在speech recognition和computer vision中，都已經有了突破性的進展，但NLP還是頗爛。  

Applications:  
- Sentiment Analysis -> 透過RecursiveNN  
- Dialogue Agent -> 透過Recurrent Neural Network  
- Machine Translation -> 透過wordvec + deep，目前做得最成功的NLP問題  

最後來點平衡報導好了：https://medium.com/@culurciello/the-fall-of-rnn-lstm-2d1594c74ce0

## Lecture 2 | Word Vector Representations: word2vec
>[Slides](http://web.stanford.edu/class/cs224n/lectures/lecture2.pdf)  
>Suggested Readings:  
[Word2Vec Tutorial - The Skip-Gram Model](http://mccormickml.com/2016/04/19/word2vec-tutorial-the-skip-gram-model/)  
[Distributed Representations of Words and Phrases and their Compositionality](http://papers.nips.cc/paper/5021-distributed-representations-of-words-and-phrases-and-their-compositionality.pdf)  
[Efficient Estimation of Word Representations in Vector Space](http://arxiv.org/pdf/1301.3781.pdf)  

在使用word2vec之前，人們用各種WordNet來建立word之間的關係，數值表達方法只能one-hot coding，每個字都是orthogonal，缺點：one-hot coding無法表現字與字之間的關聯性。  

而現在要為每一個word找一個representation(vector)，來真正代表那個word，使用的方法是distributional similarity，也就是跟據此word在字句中出現的上下文關係，來給定他的vector，使得有相近功用的字有相近的vector。  

要怎麼做呢，其實有很多方法，最著名的是Mikolov在2013年提出的[Word2vec](https://arxiv.org/pdf/1301.3781.pdf)，他是做一個Fake task(從中央字預測旁邊字or從旁邊字預測中央字)，藉此得到每一個word的vector represntation，好一個借力使力的方法。此算法有兩種，skip-gram和continuous bag-of-words(CBOW)，這堂課先介紹第一種skip-gram。 

![sg](https://i.imgur.com/QimTJdK.png)

skip-gram就是train一個NN，從中央字預測旁邊字，也就是你要在茫茫字海之中，預測每一個字會出現在你身邊(window內的範圍)的機率。  

![skip-gram](https://i.imgur.com/DIwtQju.png)

而當中的NN架構是，input跟output都是one-hot的word vector，也就是超級大維度的稀疏向量。中間經過兩個矩陣轉換，但其實我們可以用另一個角度想他，也就是把第一個矩陣當成 center word 的 embedding層(因為one-hot乘上一個矩陣就是挑出一個column的意思)，第二個矩陣想成 context word 的embedding層。  

原本你是要把 center word 的 embedding vector $(v_c)$，跟第二個矩陣做矩陣相乘，就能得到所有茫茫字海出現在你身邊的機率分布，但這樣其實運算量超級大；但是當你只關心其中一個字的output機率時，你所做的就是把 center word 的 embedding vector，跟第二個矩陣的某一個row做內積而已，內積出來就是單一字的機率，這樣來想，第二個矩陣的每一個row就是單一字的 context word vector。

![uv](https://i.imgur.com/BndhLSp.png)

所以說，在這個假設裏頭，每一個字有兩個vector來代表他，一個是 center word vector$(v_i)$，另一個是 context word vector$(u_i)$，最後我們取來用的是 center word vector$(v_i)$。

所以Word2vec其實就是做一個大型的Matrix Factorization問題，上下文中在window內的字使他target是1，其他不相干的字target是0，就結束了。

以上聽起來很棒，但這個矩陣的最佳解是怎麼找到的呢？當然是deep一波，用gradient descent下去找，那gradient descent的目標是甚麼呢？目標是最大化下面這個函數：

$$Likelihood = L(\theta) = \prod_{t=1}^T \prod_{-m \leq j \leq m, j \neq 0} P(w_{t+j} \vert w_t ; \theta)$$

其中m就是你的window大小，也就是說，在你window內的字，你希望他們的機率是最大(也就是等於一)。

可是ML工程師都偏好最小化問題，於是我們把它取log加負號，取log之後，相乘就變成相加了，更好處理：

$$J(\theta) = -\frac{1}{T} log(L(\theta)) = -\frac{1}{T} \sum_{t=1}^T \sum_{-m \leq j \leq m, j \neq 0} log P(w_{t+j} \vert w_t ; \theta)$$

而在這裡，$P(w_{t+j} \vert w_t ; \theta)$是如何算出的呢？這裡我們使用每個維度的output算softmax得到機率分布，也就是說：

$$P(o \vert c ; \theta) = \frac{exp({u_o}^T v_c)}{\sum_{w=1}^V exp({u_w}^T v_c)}$$

(其中$o = w_{t+j}, c = w_t$)


所以咧，現在就是個loss function = $J(\theta)$的最佳化問題，要gradient descent要算偏微分，於是乎：

$$\dfrac {\partial }{\partial v_c} J(\theta) = -\frac{1}{T} \dfrac {\partial }{\partial v_c}  log(L(\theta)) = -\frac{1}{T} \sum_{t=1}^T \sum_{-m \leq j \leq m, j \neq 0} \dfrac {\partial }{\partial v_c}  log P(o \vert c ; \theta)$$

先單獨算一下$\dfrac {\partial }{\partial v_c}  log P(o \vert c ; \theta)$這個東西是甚麼：

$$\dfrac {\partial }{\partial v_c}  log P(o \vert c ; \theta) = \dfrac {\partial }{\partial v_c}  log \frac{exp({u_o}^T v_c)}{\sum_{w=1}^V exp({u_w}^T v_c)}$$

log 相除變相減：

$$\dfrac {\partial }{\partial v_c}  log \frac{exp({u_o}^T v_c)}{\sum_{w=1}^V exp({u_w}^T v_c)} = \dfrac {\partial }{\partial v_c} {u_o}^T v_c - \dfrac {\partial }{\partial v_c} log \sum_{w=1}^V exp({u_w}^T v_c)$$

很明顯，第一項是：$\dfrac {\partial }{\partial v_c} {u_o}^T v_c = u_o$

第二項，使用chain rule：

$$\dfrac {\partial }{\partial v_c} log \sum_{w=1}^V exp({u_w}^T v_c) = \frac{1}{\sum_{w=1}^V exp({u_w}^T v_c)} \sum_{x=1}^V exp({u_x}^T v_c)u_x$$

chain rule出來的項，我們使用新變數$x$。然後其實，前面那項可以塞進去後面，塞進去之後，就會發現它其實是在算softmax，也就是：

$$\frac{1}{\sum_{w=1}^V exp({u_w}^T v_c)} \sum_{x=1}^V exp({u_x}^T v_c)u_x = \sum_{x=1}^V \frac{exp({u_x}^T v_c)}{\sum_{w=1}^V exp({u_w}^T v_c)} u_x$$

$$ = \sum_{x=1}^V softmax({u_x}^T v_c) u_x = \sum_{x=1}^V P(x \vert c) u_x$$

現在合併一二項：

$$\dfrac {\partial }{\partial v_c} J(\theta) = u_0 - \sum_{x=1}^V P(x \vert c) u_x$$

$u_o$就是出現在附近的上下文的context embedding vector，$\sum_{x=1}^V P(x \vert c) u_x$則是所有茫茫字海中，所有人的context embedding vector機率加權平均(也就是期望值)，理想上只有$P(x \vert c)$會是1，其他都是0，所以train到理想時，$u_o = \sum_{x=1}^V P(x \vert c) u_x$，梯度接近0，合理。

其實context embedding那邊也應該要算$J(\theta)$，但會差不多，所以就不算了。








