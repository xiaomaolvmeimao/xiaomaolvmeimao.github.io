---
layout:     post
title:      Robust Chinese Word Segmentation with Contextualized Word Representations
author:     Jexus
tags: 		NLP deep_learning
subtitle:   DSP 期末專題 State of the art 中文斷詞
category:  project
---

> 這是我 DSP 2018 Fall 數位語音處理概論課程的期末專題，報告已經放上 arXiv 可以直接看PDF檔。  
> arXiv: https://arxiv.org/abs/1901.05816  
> 與目前檯面上常見的斷詞系統 [Jieba](https://github.com/fxsjy/jieba) (HMM-based) 及 [CKIP](http://ckipsvr.iis.sinica.edu.tw/) (rule-based) 相比，此系統已經勝過這兩者許多，因此我把較好的 model 及程式碼整理過後包成一個開源的 Library  
> github: https://github.com/voidism/pywordseg  
>  也放到 PyPI 上面，使用pip的話只要在terminal直接輸入 `pip install pywordseg` 就可以安裝使用  
> PyPI: https://pypi.org/project/pywordseg/  
> 在第一次使用的時候會把 model 自動下載下來，需要等待約一分鐘。

# Robust Chinese Word Segmentation with Contextualized Word Representations

## Abstract

In recent years, after the neural-network-based method was proposed, the accuracy of the Chinese word segmentation task has made great progress. However, when dealing with out-of-vocabulary words, there is still a large error rate. 

We used a simple bidirectional LSTM architecture and a large-scale pretrained language model to generate high-quality contextualize character representations, which successfully reduced the weakness of the ambiguous meanings of each Chinese character that widely appears in Chinese characters, and hence effectively reduced OOV error rate. State-of-the-art performance is achieved on many datasets. 

## 1 Introduction

For Chinese word segmentation task, many works have explored novel neural-network-based meth- ods to improve the accuracy of segmentation pre- diction (Pei et al., 2014; Ma and Hinrichs, 2015; Zhang et al., 2016a; Liu et al., 2016; Cai et al., 2017; Wang and Xu, 2017; Ma et al., 2018). 

However, most of them used the context- independent character representations, or used in- formation about neighboring words, such as us- ing bigram embedding as input (Ma et al., 2018). These context-independent character representations may cause weaknesses in the entire word segmentation model. Because in Chinese, a large number of characters have different meanings in different words, hence their true meaning cannot be determined unless they are combined together with other characters. 

When the context-independent word representations are applied to the word segmentation model, the model may be able to memorize all the combinations of characters that should be combined into words if all the words have been identified in the training set, even if these characters are not truly represented by the vectors. However, if it is an out-of-vocabulary word that only appears in the testing set, this becomes a challenge for the model. 

In the discussion of previous works (Ma et al., 2018; Huang and Zhao, 2007), the out-of-vocabulary issue was blamed for being one of the main sources of error rates. This issue will have a great impact on the practicality of using the trained model in practical applications. Because the proportion of OOV issue is relatively small in the dataset, a simple neural-network architecture may achieve a good accuracy rate in these datasets, but it does not perform well in practical applications. 

However, after the emergence of contextualized word representations (Peters et al., 2018), this weakness may be effectively reduced. In the following paragraphs, we will compare the significant advances in contextualized word representations to reducing the OOV error rate. 

![Figure 1](https://i.imgur.com/FR9869Y.png)

> Figure 1: Bi-LSTM models: Blue circles are input (from ELMo) embeddings. Orange and green squares are LSTM cells, repeated for three times. C/S is a 2- way softmax to determine continue or separate at every time step.

## 2 Model

### 2.1 Bi-LSTM model

Our model is based on long short-term memory neural networks. Different from the previous state-of-the-art model (Ma et al., 2018) with stacked LSTMs, we use non-stacked, three layers of Bi-LSTMs(see Figure 1). The input vectors are from ELMo embedding, which will be introduced in the following section. The output softmax layer predicts two possible states: continue or separate from transition based model. The meaning of continue state is to wait for next character to append, while the meaning of separate state is to combine the currently collected characters into a word. This operation is similar to the method used in (Zhang et al., 2016a). An example is demonstrated in Table 1.

![](https://i.imgur.com/PRV2pVY.png)

> Table 1: An easy example for the output of transition based model. ”1” represents ”separate”, and ”0” represents ”continue”.

There are some remained hyperparameters: 300 units for LSTM hidden size, 1024 for the dimension of ELMo embedding, 0.33 for the dropout rate at the output of each LSTM layer except the top layer. We applied cross entropy to compute the loss of the softmax output with the true label. We use Adam (Kingma et al., 2014) to optimize the model with learning rate $α = 10^{−3}, β_1 = 0.9 ,β_2 = 0.999, ε = 10^{−8}$.

### 2.2 ELMo

Different from traditional word embedding con- cepts, in ELMo (Peters et al., 2018), word repre- sentation is a function of the sentence. That is, the vector representation of every word is not de- termined until the whole sentence is constructed, and thus each vector representation is contextu- alized. To get the contextualized representation, they use a bidirectional language models (biLM) and have it trained on large corpus.. The forward LM models the probability of token $t_k$ given the history ($t_1, ..., t_{k−1}$):

$$p(t_1, t_2, \ldots, t_N) = \prod_{k=1}^N p({t_k} \mid t_1, t_2, \ldots, t_{k-1}).$$

A backward LM runs in reverse, predicting the previous token given the future context:

$$p(t_1, t_2, \ldots, t_N) = \prod_{k=1}^N p(t_k \mid t_{k+1}, t_{k+2}, \ldots, t_N).$$

Starting from computing a context-independent token representation 
$$x^{LM}_{k}$$
 (via a CNN over characters) for each token 
 $$t_k$$
 , we pass 
 $$x^{LM}_k$$
  through two layers of Bi-LSTMs. At each position k, we get a context-dependent representation 
  $$\overrightarrow{\mathbf{h}}^{LM}_{k,1}$$
   and 
 $$\overrightarrow{\mathbf{h}}^{LM}_{k,2}$$
  from the two layers of forward LSTMs, as well as 
  $$\overleftarrow{\mathbf{h}}^{LM}_{k,1}$$
   and 
   $$\overleftarrow{\mathbf{h}}^{LM}_{k,2}$$
    from the two layers of backward LSTMs. The outputs of the top layers (
  $$\overrightarrow{\mathbf{h}}^{LM}_{k,2}$$
  , 
  $$\overleftarrow{\mathbf{h}}^{LM}_{k,2}$$
  ) are used to predict next/previous token respectively by performing a Softmax layer with tied parameters for both directions. Finally, we jointly maximize the log likelihood of both the forward and backward directions:

$$ \sum_{k=1}^N (\log p({t_k} \mid t_1, \ldots, t_{k-1}; \Theta_x, \overrightarrow{\Theta}_{LSTM}, \Theta_s)$$

$$ + \log p({t_k} \mid t_{k+1}, \ldots, t_{N}; \Theta_x, \overleftarrow{\Theta}_{LSTM}, \Theta_s))$$

To get the final word representation from the pretrained biLM for the downstream task, we compute:

$$\mathbf{ELMo}_k = \gamma (s_0 \mathbf{x}^{LM}_{k} + s_1 \mathbf{h}^{LM}_{k,1} + s_2 \mathbf{h}^{LM}_{k,2})$$

where $\gamma$ and $s_i$ can be adjusted when training on different downstream tasks. $\mathbf{s}_{i}$ are softmax-normalized weights and the scalar parameter $\gamma$ allows the task model to scale the entire ELMo vector. 

In our task, we train the ELMo model with the sentences separated into characters, in order to get better character representations. This operation will make the character CNNs in front of the ELMo model degenerate into some simple DNNs. However, by doing this, we can maintain the consistency between training and testing the ELMo model, and get more appropriate representations for every character. 

## 3 Experiments 

### 3.1 Data

We run our experiments on datasets of SIGHAN 2005 bake-off task (Emerson, 2005) and a dataset of Chinese Universal Treebank (UD) from the Conll2017 shared task (Zeman et al., 2017), both with the official data split. Table 3 shows statistics of each data set. We randomly select 5% training set as the validation set while training. We convert MSR and PKU dataset to traditional Chinese. Following Ma et al. (2018), we train and evaluate a model for each of the dataset, rather than train one model on the union of all dataset. We also convert all digits, punctuation and Latin letters to half- width for maintaining the consistency between the training and testing sets.

![](https://i.imgur.com/vJaVF8P.png)

> Table 3: Statistics of the dataset.

For preparing the corpus for training embeddings, we made a union corpus composed of five training sets (about 22.8M tokens) and randomly sampled sentences from Chinese gigaword (about 7.8M tokens), exactly two million sentences in total. All the sentences are separated into characters. We use this corpus to train a simple skip- gram Word2Vec embedding (Mikolov et al., 2013) for our baseline model. And we train an ELMo model for 10 epochs on the same corpus, used by our main model.

### 3.2 Results

We compare our model with the state-of-the-art results from recent neural network based models, together with existing word segmentation toolkits Jieba and CKIP (Hsieh et al., 2012) (see Table 2). We achieve the best results on all datasets.

![](https://i.imgur.com/Deag1F3.png)

> Table 2: The state of the art performance on different datasets. We also compare with existing word segmentation toolkits Jieba and CKIP (Hsieh et al., 2012).

We can notice that our baseline model with pretrained skip-gram embeddings performs good enough to be competitive with the previous state-of-the-art results. However, when discussing the OOV error rate in the next section, we can see the significant difference between the baseline model and the main model. We also tested a generally pretrained Traditional Chinese ELMo model provided by HIT-SCIR (Che et al., 2018), trained on Wikipedia, which performed slightly worse than our main model.

### 3.3 Progress on OOVs

In all the datasets, there is a certain proportion of words only shown in the testing set, but not shown in the training set, namely, out-of-vocabulary (OOV). Thus, the model can not just remember the combinations of all the words in the training set to perform well on OOV words. It must have the ability to generalize the knowledge to combine characters into words on the testing set.

![](https://i.imgur.com/9M5xugs.png)

> Table 4: Testing set OOV rate, together with the accuracy achieved on OOV set by our baseline model and our main model, respectively.

In Table 4 we show the statistics of the OOV rate among these datasets and compare the performance between our main model and baseline model. We can see that even though the baseline model can have a good accuracy nearly 97% on the whole testing sets of these datasets, the accuracy computed only on OOV words is still stuck in nearly 91%. However, our main model still maintains relatively good performance on OOV words outperforming the baseline model with at most about 5% accuracy, showing the robustness of our model.

Nevertheless, we notice that the accuracy of MSR dataset is not progressed much compared to the baseline model. We observe that the MSR dataset has more consistency between the training set and testing set with low OOV rate (2.6%), as mentioned by (Ma et al., 2018). However, many OOVs come from strings with digits in Chinese, such as ”15類”, ”458家”, ”2686個”, ”去年6月”, ”4月19日”. Other OOVs largely come from name entities, such as ”蚌埠檸檬酸廠”, ”靖邊天然氣 淨化廠”, ”長江水文局”, ”中共湖北省委”, all of them are tied to be single words. In these situations, our main model can not make full use of its advantages, because these words are more likely to be segmented in that way by human defined rules instead of its natural semantic meaning. When dealing words with digits, it is easy for both model; when dealing with longer name entities, our model may segment them into smaller pieces while our baseline model also does that. Hence, testing on MSR dataset does not cause obviously different results comparing to the baseline model.

![](https://i.imgur.com/VX7URyB.png)

> Table 5: Testing set OOV rate, together with the ratio of OOV with digits and ratio of OOV with long words, respectively.

To prove this assumption is true, we made some statistics on the testing data (see Table 5). We counted all words with digits in it in the OOV set of each testing data. The MSR dataset has about 20% OOV words with digits in it, while other datasets only have the ratio of less than 10% OOV words with digits in it. We can not identify all name entities in OOV set directly, however, we noticed that long words are more likely to be name entities in Chinese, especially when words are not shorter than 5 characters. As a result, we counted all words with their length ≥ 5 (see Table 5), and prove that MSR indeed has larger (about 36%) OOV words with their length ≥ 5, while other datasets only have the ratio of less than 10% OOV words with their length ≥ 5.

### 3.4 Other discussions

When looking into some error segmented words, we find lots of words are ambiguous to decide whether to segment it into small segments, which is the thing our model usually do. For example, ”老歌” (old song), ”多次” (many times), ”抗癌” (against cancer) can be segmented to smaller units as ”老 歌”, ”多 次”, ”抗 癌” without breaking their semantic meaning. However, our evaluation metrics are not taking this into account. We only care how data is annotated, so that all this kind of errors contribute to the error rate, which is not a fair way of evaluating the performance. On the other side, human evaluation can be more accurate, but it is more expensive and time-consuming. The further improvement on this issue may be useful for Chinese word segmentation task.

## 4 Conclusion

In this work, we used a simple Bi-LSTM model together with embedding from language models (ELMo), achieving state-of-the-art performance on many Chinese word segmentation datasets. Also, we found that our model is more robust when dealing with the out-of-vocabulary issue, showing that our model can perform better when applied to practical applications.

## References

- Ma, Ji and Ganchev, Kuzman and Weiss, David. 2018. State-of-the-art Chinese Word Segmentation with Bi-LSTMs. In Proceedings of the 2018 Conference on Empirical Methods in Natural Language Processing. Association for Computational Linguistics.
- Peters, Matthew E. and Neumann, Mark and Iyyer, Mo- hit and Gardner, Matt and Clark, Christopher and Lee, Kenton and Zettlemoyer, Luke. 2018. Deep contextualized word representations In Proceedings of the 2018 Conference of the North American Chapter of the Association for Computational Linguistics: Human Language Technologies. Association for Computational Linguistics.
- Diederik P. Kingma and Jimmy Ba. 2014. Adam: A Method for Stochastic Optimization. In Proceedings of the 2015 Conference on International Conference for Learning Representations. CoRR, abs/1412.6980.
- Deng Cai, Hai Zhao, Zhisong Zhang, Yuan Xin, Yongjian Wu, and Feiyue Huang. 2017. Fast and accurate neural word segmentation for Chinese. In Proceedings of the 55th Annual Meeting of the Association for Computational Linguistics (Volume 2: Short Papers), pages 608–615. Association for Computational Linguistics.
- Xinchi Chen, Zhan Shi, Xipeng Qiu, and Xuanjing Huang. 2017. Adversarial multi-criteria learning for Chinese word segmentation. In Proceedings of the 55th Annual Meeting of the Association for Computational Linguistics (Volume 1: Long Papers), pages 1193–1203, Vancouver, Canada. Association for Computational Linguistics.
- Chang-ning Huang and Hai Zhao. 2007. Chinese word segmentation: A decade review. 21(3):8.
- Shuhei Kurita, Daisuke Kawahara, and Sadao Kuro- hashi. 2017. Neural joint model for transition-based chinese syntactic analysis. In Proceedings of the 55th Annual Meeting of the Association for Computational Linguistics (Volume 1: Long Papers), pages 1204–1214. Association for Computational Linguistics.
- Yijia Liu, Wanxiang Che, Jiang Guo, Bing Qin, and Ting Liu. 2016. Exploring segment representations for neural segmentation models. CoRR, abs/1604.05499.
- Jianqiang Ma and Erhard Hinrichs. 2015. Accurate linear-time Chinese word segmentation via embedding matching. In Proceedings of the 53rd Annual Meeting of the Association for Computational Linguistics and the 7th International Joint Conference on Natural Language Processing (Volume 1: Long Papers), pages 1733–1743, Beijing, China. Association for Computational Linguistics.
- Tomas Mikolov, Kai Chen, Greg Corrado, and Jef- frey Dean. 2013. Efficient estimation of word representations in vector space. arXiv preprint arXiv:1301.3781.
- Wenzhe Pei, Tao Ge, and Baobao Chang. 2014. Max-margin tensor neural network for Chinese word segmentation. In Proceedings of the 52nd Annual Meet- ing of the Association for Computational Linguistics (Volume 1: Long Papers), pages 293–303. Association for Computational Linguistics.
- Xian Qian and Yang Liu. 2017. A non-dnn feature engineering approach to dependency parsing – fbaml at conll 2017 shared task. In Proceedings of the CoNLL 2017 Shared Task: Multilingual Parsing from Raw Text to Universal Dependencies, pages 143–151. Association for Computational Linguistics.
- Chunqi Wang and Bo Xu. 2017. Convolutional neural network with word embeddings for Chinese word segmentation. CoRR, abs/1711.04411.
- Jie Yang, Yue Zhang, and Fei Dong. 2017. Neural word segmentation with rich pretraining. In Proceedings of the 55th Annual Meeting of the Association for Computational Linguistics (Volume 1: Long Papers), pages 839–849. Association for Computational Linguistics.
- Yu-Ming Hsieh, Ming-Hong Bai, Jason S Chang, and Keh-Jiann Chen. 2012. Improving PCFG Chinese parsing with context-dependent probability reestimation. In CLP 2012, page 216.
- Daniel Zeman, Martin Popel, Milan Straka, Jan Ha- jic, Joakim Nivre, Filip Ginter, Juhani Luotolahti, Sampo Pyysalo, Slav Petrov, Martin Potthast, Fran- cis Tyers, Elena Badmaeva, Memduh Gokirmak, Anna Nedoluzhko, Silvie Cinkova, Jan Hajic jr., Jaroslava Hlavacova, Va ́clava Kettnerova ́, Zdenka Uresova, Jenna Kanerva, Stina Ojala, Anna Mis- sila ̈, Christopher D. Manning, Sebastian Schuster, Siva Reddy, Dima Taji, Nizar Habash, Herman Le- ung, Marie-Catherine de Marneffe, Manuela San- guinetti, Maria Simi, Hiroshi Kanayama, Valeria de- Paiva, Kira Droganova, He ́ctor Mart ́ınez Alonso, C ̧ag ̆rı C ̧o ̈ltekin, Umut Sulubacak, Hans Uszkor- eit, Vivien Macketanz, Aljoscha Burchardt, Kim Harris, Katrin Marheinecke, Georg Rehm, Tolga Kayadelen, Mohammed Attia, Ali Elkahky, Zhuoran Yu, Emily Pitler, Saran Lertpradit, Michael Mandl, Jesse Kirchner, Hector Fernandez Alcalde, Jana Str- nadova ́, Esha Banerjee, Ruli Manurung, Antonio Stella, Atsuko Shimada, Sookyoung Kwak, Gustavo Mendonca, Tatiana Lando, Rattima Nitisaroj, and Josie Li. 2017. Conll 2017 shared task: Multilingual parsing from raw text to universal dependencies. In Proceedings of the CoNLL 2017 Shared Task: Multilingual Parsing from Raw Text to Universal Dependencies, pages 1–19, Vancouver, Canada. Association for Computational Linguistics.
- Meishan Zhang, Yue Zhang, and Guohong Fu. 2016a. Transition-based neural word segmentation. In Proceedings of the 54th Annual Meeting of the Association for Computational Linguistics, ACL 2016, August 7-12, 2016, Berlin, Germany, Volume 1: Long Papers.
- Hao Zhou, Zhenting Yu, Yue Zhang, Shujian Huang, XIN-YU DAI, and Jiajun Chen. 2017. Word-context character embeddings for Chinese word segmentation. In Proceedings of the 2017 Conference on Empirical Methods in Natural Language Processing, pages 760–766, Copenhagen, Denmark. Association for Computational Linguistics.
- Wanxiang Che, Yijia Liu, Yuxuan Wang, Bo Zheng, and Ting Liu. 2018. Towards better UD parsing: Deep contextualized word embeddings, ensemble, and treebank concatenation. In Proceedings of the CoNLL 2018 Shared Task: Multilingual Parsing from Raw Text to Universal Dependencies, pages 55–64, Brussels, Belgium. Association for Computational Linguistics.
- Murhaf Fares, Andrey Kutuzov, Stephan Oepen, and Erik Velldal. 2017. Word vectors, reuse, and replicability: Towards a community repository of large-text resources. In Proceedings of the 21st Nordic Conference on Computational Linguistics, pages 271– 276, Gothenburg, Sweden. Association for Computational Linguistics.

