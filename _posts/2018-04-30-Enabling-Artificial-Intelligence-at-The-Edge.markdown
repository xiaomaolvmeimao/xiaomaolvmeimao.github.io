---
layout:     post
title:      Enabling Artificial Intelligence at The Edge
author:     Jexus
tags: 		Speech Artificial_Intelligence Edge_Computing
subtitle:   2018/04/30 intelliGo意騰科技CTO 邊緣運算 - 演講速記
category:  note
---

Enabling Artificial Intelligence at The Edge
===
時間：4/30 (一) 16:30~17:20  
地點：明達館231室  
講者：  
陳宗樑 CTO, 意騰科技 (美國史丹佛大學電機工程所博士)  
陳宏慶 Chip Architect, 意騰科技 (國立交通大學電子工程所碩士)  

![](https://i.imgur.com/JtMZciK.png)


## intelliGo 
2016 成立(從MediaTek分出來)  
只有40 people, 75%是R&D  
die size high performance 低功耗  
2017 推出 Debussy 晶片   


## Noise reduction by DNN
用full connected NN 做語音雜訊消除  

**Traditional Assumption:(舊方法是做一堆假設)**  
- uncorrelated signal <-> noise(假設雜訊跟我們要的聲音是獨立的)  
- gaussian noise (假設雜訊是個Gaussian)  
等等...  

**DNN -> no assumption!(現在用了NN，甚麼假設都免了，直接開train)**

目前做到是 real time (10 ms 內完成去噪，已經是產品級)  

講者有現場Demo，就是有好幾段各種雜音存在的speech，像是背景有機關槍聲音，菜市場聲音...等等，濾完真的就很乾淨沒噪音  

## Device ai (相對於cloud ai)
**優點**
- security 
- power efficiency
- low latency

training still on GPU(now)  
inference at the edge  

未來可能可以做到training at the edge  

## small example  
架一個簡單的Noise reduction DNN  
**參數量：**  
15M coefficients => 60MB  

**運算需求：**   
memory read speed => 6GB/sec  
MAC computation => 1.5 G MACs/sec  
> 這裡有人問說如果晶片只是做inference的功能，參數不需要更新，只需要load進來一次就好了，根本沒有memory read speed的要求吧，架構師回答，基本上低功耗晶片的cache不夠大，不可能這樣做，目前市面上做得最好的低功耗晶片也達不到

**功耗：**  
power consumption 主要由 memory access 決定  
45nm CMOS process  
MAC mem access -> 1.5G\*2\*640pJ ~ 2W  
功耗頗大  

**各家產品比較：**  
Google TPU 為標準  
Debussy "效率"是其24倍 (運算速度除以所耗能量)  

## Technical outlook
CNN is right representation?  
CNN model不一定真的能解決所有computer vision問題(例：五官對調的臉，在CNN來看會的到一樣的結果，因為它沒有做feature之間相對位置的處理)  
Hinton => 提出Capsules Net，也許能解決此問題  
其實也就是回到人們辨識影像的觀點，其實就是還沒有CNN之前人們做影像識別所走的老路，想辦法讓CNN跟其結合  
=> Model based 結合 Neural based  

其他各家提出的  
Deepmind => popuation based training  

#### 對於Optimization
Deep Compression (stanford)  
Depth-seperable CNN (google) => 先做2D再做1D  

### Architecture(AI on 硬體)
現在主要就是兩條路：  
- Domain Specific Arch
- Dataflow Processing Arch

重點是要解決energe efficiency問題  
量級思維 => Kilo to Mega  
想把energe efficiency提昇一個數量級  

類比運算是 mixed signal computing & low power consuming 但是 low precise computing  
數位運算是 high precise computing  

可以將類比數位結合  
因為train neural network時不用追求分毫不差的準確度，運算有差可以當成雜訊而已就好，剛剛好可以用類比運算來做  

跟ML謀和，類比又開始走紅  
  

## Heterogeneous Integration
異質性integration  
intelliGo 做的 麥克風晶片   
跟一元上面"圓"一樣大  

### more than moore  
舊思維 => 蓋平房 只要用一個光罩  
現在 => 往上蓋  
AI chip => 小量多樣 多功能  
再Soc起來  

### Computing in memory
![](https://i.imgur.com/GkThcm2.jpg)

就是剛剛類比講的  
用電容加減取代 full adder 乘法器 等等...  
如果是用數位運算 SRAM去讀的時間 用類比電容運算可以做十次乘法  
但用電容會有mismatch non-linear 問題  
不過如果是Deep 沒關係 就當噪音就好  

### 有意思的一段話：

<center><i>SW is eating the world.</i></center>
<center><i>AI is eating SW.</i></center>
<center><i>EE is bringing AI up and up.</i></center>


## Energe Efficiency
![](https://i.imgur.com/U4lgxD6.jpg)

就是要比較 給你一瓦電 可以做多少 TOPS  

## QA:
**Q1. 前面的Noise Reduction在訓練的時候，label data是怎麼來的? 如果是直接混合不會overfitting嗎?**  

就是把一般speech自己疊上噪音來的。  
但雜音的資料非常多樣，是上萬等級的多，基本上不太容易overfitting。  

**Q2. 前面的Noise Reduction的結果Demo，是在train set上面還是testing set 上面?**  

是testing set，基本上這已經是當產品賣了，不可能還只有做training set測試  

**Q3. local training?**    

未來可能會做  

## Appendix:
錘子科技2018在北京鳥巢的新品發表會   
其中有展示意騰的AI降噪晶片  
影片從 56:49 開始到 58:17  
<iframe width="560" height="315" src="https://www.youtube.com/embed/4XAiTkTGSi4?start=3409" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


<!-- https://youtu.be/4XAiTkTGSi4?t=3409 -->