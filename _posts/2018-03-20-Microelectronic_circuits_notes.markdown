---
layout:     post
title:      Microelectronic Circuits - Course Notes
author:     Jexus
tags: 		Electronics Microelectronic_Circuits
subtitle:   電子學筆記 - 呂良鴻<電子學二>
category:  note
---


![cover](https://i.imgur.com/hjSwkM5.png)

### Ch7: Building blocks of integrated-circuit amplifiers (7.1-7.6) 

[Lecture Ch7 PDF](http://cc.ee.ntu.edu.tw/~lhlu/eecourses/Electronics2/Electronics_Ch7.pdf)
[Backup](https://www.dropbox.com/s/964p8z0haqeiirr/Electronics_Ch7.pdf?dl=0)

在學電子學一時，我們所用的設計想法都是以離散電路出發，也就是把各個元件放在麵包板上連接的概念。然而這章開始，我們需要改用積體電路的想法來設計電路。

![Difference between Discrete Circuits and Integrated Circuits](https://i.imgur.com/GDRGUrh.png)
> http://www.elprocus.com/elprocus-staging/difference-between-discrete-circuits-integrated-circuits/

積體電路與離散電路有何差別呢？在晶圓上中製造一個大電阻的成本及空間使用很高，反而製造(在矽晶上摻雜)一個電晶體的成本已經很小很小很小，且空間也只占用幾奈米。

為了製造一個放大倍數大一點的放大器，必須有一個大電阻當負載。然而製造大電阻除了上述成本&空間問題，電阻太大也會使直流DC偏壓點跑掉，會讓人頭很痛。所以我們突發奇想，剛好可以用電晶體在AC下會出現的 ${r_o}$ (爾利效應，only AC電阻很大)來取代大電阻，這樣也就不會影響到DC偏壓點，一舉兩得。
![active load](https://i.imgur.com/sOyxWuG.png)

雖然這招順利的讓負載電阻變大，但如果這個放大器本身的輸出阻抗($R_{out}$)比負載電阻小很多，兩個一大一小並聯下去，還是會得到小的電阻，怎麼辦呢？

如果我們單純想辦法放大放大器內部的$R_{out}$，可能會使電流($i_{sh}$)變小，放大倍率還是變小，得不償失，所以我們又把腦筋動到電晶體上，我們在原本的CS放大器上再接一個CG結構的電晶體(這招叫做cascode)，它的效果是current buffer，他不放大信號，但能把電流百分之百從input端拉到output端，因此電流不會變小，同時他自己也有個AC電阻很大的$r_o$，剛好可以把$R_{out}$放大，又是一舉兩得。
![cascode](https://i.imgur.com/aie07zZ.png)

了解完AC信號放大的部分，我們還得知道DC偏壓要怎麼弄到適當的偏壓點，因此開始介紹current mirror結構。
![current mirror](https://i.imgur.com/jLYOlKZ.png)

current mirror的目標是希望做出一個理想電流源，也就是一個圈圈中間一個箭頭的那個東西，但其實理想電流源的小${r_o}$是無限大，但current mirror不是，因此我們首先介紹 cascade current mirror 結構，讓${R_o}$增大。
![cascade current mirror](https://i.imgur.com/ihn3zrY.png)

另外，BJT的β值不夠大時，參考電流 $I_{ref}$ 跟輸出電流 $I_o$ 的比例並不好拿捏(通常電晶體的β值是一個range，沒有確定值)，我們希望降低β值對輸出電流的影響，於是在BJT的current mirror中多加一個BJT，作為base current compensation的效果。
![base current compensation](https://i.imgur.com/KcpJ9f7.png)

再來，我們希望剛剛講的這兩件事(讓$R_o$增大、降低β值的影響)可以實現在同一個 current mirror 結構中，於是我們介紹Wilson current mirror結構，他雖然$R_o$會比 cascode current mirror 小二分之一(影響還好)，但β值的影響也一樣地有被降低。
![Wilson current mirror](https://i.imgur.com/2NUjljL.png)

不過Wilson current mirror結構用在MOS就效果沒那麼顯著，因為MOS不會有β值的問題要解決，$R_o$也跟原本cascode的時候一樣，反而因為一邊有接兩個電晶體，一邊只有一個，會讓兩邊$V_{DS}$不同，所以我們又把第四顆電晶體放回去，結果這樣就跟原本cascode有87%像了。
![Wilson current mirror - MOS](https://i.imgur.com/da7AB7x.png)

最後，我們介紹Widlar current source結構，為了輸出一個大小是比參考電流 $I_{ref}$ 小很多倍的電流(可能百分之一)，但我們不可能做到兩顆電晶體channel的長度差是100倍，那樣太浪費空間。於是我們在current mirror的右下角多加一個電阻$R_E$，這樣 $I_{ref}$ 跟 $I_o$ 的比例便能由$R_E$來決定了，雖然$R_E$也不小就是，但設計IC就是不斷的compromise啊。
![Widlar current source](https://i.imgur.com/KQ1WaP5.png)

### Ch8: Differential and multistage amplifiers (8.1 - 8.6.1) 

[Lecture Ch8 PDF](http://cc.ee.ntu.edu.tw/~lhlu/eecourses/Electronics2/Electronics_Ch8.pdf)
[Backup](https://www.dropbox.com/s/xsf4n4uei9y5ego/Electronics_Ch8.pdf?dl=0)

這節開始我們要介紹差動放大器，也就是把前面所介紹的放大器，兩個一對的接在一起。這樣接有甚麼好處呢？因為實際上電路在工作的時候，必定會產生許許多多的雜訊，這樣直接放大訊號的話，連雜訊也一起被放大了，頭會很痛。如果訊號是由兩條電線所傳輸，用他們兩條線電位之間的差值來傳遞真正的訊號，這樣的話就算有雜訊，也是兩條都有(因為兩條電路靠得很近)，經過相減之後，雜訊就不見了，有沒有好棒棒啊。


![DA](https://i.imgur.com/C08yfuX.png)


但實際上的運作方法當然不只這樣，差動放大器的基本結構是把兩個CS放大器的接地端接到同一個電流源上，也就是說，各自兩個放大器通過的總電流是固定的，如此一來，如果放大器A拿了很多電流，放大器B就只能拿少少的電流，反之亦然。
![DA plot](https://i.imgur.com/MfffzGU.png)

那麼可以想像一下，如果兩個放大器的輸入訊號完全相同，那麼他們各自的通過電流也會一模一樣，因為完全是對稱的，剛好每個放大器分到總電流的二分之一，剛好在這個情況底下，訊號的增益是小的，而如果兩個放大器的輸入訊號差異較大，訊號的增益會是大的。而我們其實可以把兩個不同輸入信號的組合，拆分成同樣共有的輸入(稱為common mode)，跟他們的信號差值(differential)，common mode其實就是這兩個信號的平均值，differential就是扣掉平均值之後剩下的東西。因為電路是一個線性系統，所以我們乾脆就把common mode跟differential分開兩次討論，計算之後再把結果加起來就好啦，這樣的效果會是一模一樣的(根據疊加原理)。所以囉，common mode因為是完全一樣的訊號，訊號的增益會很小(理想上是零)，反之differential的增益就很大，如此一來就完全達到我們想要得到的效果了，真的很棒吧。
![common mode + differential](https://i.imgur.com/qQkpy2t.png)

為了清楚了解common mode信號跟differential信號各自被放大的比例差異，我們定義了一個東西叫做CMRR(Common-mode rejection ratio)，他就等於differential信號的增益除以common mode信號的增益(
$CMRR = |\frac{A_d}{A_{cm}}|$)，CMRR在理想上是無限大，因為${A_{cm}}$是$0$，但實際上當然不可能這麼好，為什麼呢? 因為剛剛都是假設兩個放大器完全一模一樣的時候，common mode信號輸入才能完全被放大一模一樣的倍率，在output端common mode的輸出才會兩端無電位差，因此${A_{cm}}$等於零。然而實際上在製造積體電路時，兩個放大器還是不太可能完全一樣，一定有一些極細微的差異(稱為mismatch)。這些差異(mismatch)分為兩種，第一種是兩個放大器的負載電阻值不一樣，我們先假設一邊是$R_D$一邊是$R_D+\Delta R_D$好了(通常$\Delta R_D$會在$R_D$的$\frac{1}{100}$以內)，對於common mode信號來說，common mode信號的增益是 $A_{cm} \approx \frac{R_D}{2R_{SS}}(\frac{\Delta R_D}{R_D})$，這個值原本應該要是零的，而這對於CMRR的影響會使$CMRR = |\frac{A_d}{A_{cm}}| = |\frac{2g_mR_{SS}}{\Delta R_D/R_D}|$，因此CMRR就不再是無限大了。
![R_D mismatch](https://i.imgur.com/7kpr989.png)

不過對於differential信號來說，這樣的mismatch對它影響還好喔，它兩邊輸出的電壓差會是$v_{od}=g_mR_D(1+\frac{\Delta R_D}{2R_D})v_{id}$，因為旁邊有一個大的1，而誤差項不到百分之一，所以通常可以忽略。  

第二種mismatch是電晶體的mismatch，也就是電晶體的$g_m$不一樣，我們先假設$g_{m1} = g_m + \frac{\Delta g_m}{2}$，$g_{m2} = g_m - \frac{\Delta g_m}{2}$，那麼這樣一算下來，$A_{cm} \approx \frac{R_D}{2R_{SS}}(\frac{\Delta g_m}{g_m})$(跟剛剛長得很像吧)，而$CMRR = |\frac{A_d}{A_{cm}}| = |\frac{2g_mR_{SS}}{\Delta g_m/g_m}|$
(也是把剛剛的$R_D$換成$g_m$而已)。

剛剛忘記先說，以上講的狀況都是MOS，如果是BJT，原則上是87%像，但還是有一些不一樣喔。哪裡不一樣咧? 首先因為BJT的Collector端電流只有Emitter端的$\alpha$倍，因此在輸入信號兩邊都是DC的$V_{CM}$時，輸出信號$v_{c1} = V_{CC} - \alpha IR_C$，會跟MOS差一個$\alpha$。

![BJT](https://i.imgur.com/ZmHFdKr.png)

再來，來看一下當輸入信號兩邊也還是DC，但是是差信號的話會怎樣。因為BJT的大信號電流公式是exponential的(MOS是二次式)，變化比較劇烈，我們算出來$i_{E1}=\dfrac {I}{1+e^{-v_{id}/V_{T}}}$，而另一顆BJT的Emitter電流則對稱$i_{E2}=\dfrac {I}{1+e^{v_{id}/V_{T}}}$，我們把這兩個公式畫在圖上，橫軸放$\dfrac {v_{id}}{V_{T}}$，縱軸放$\dfrac {i_{E}}{I}$，我們注意到，當$\dfrac {v_{id}}{V_{T}} = 0$時，兩顆BTJ剛好一人分到一半電流I，而在$\dfrac {v_{id}}{V_{T}} = \pm 4$時，就已經讓其中一顆BJT拿到全部電流，另一顆幾乎沒電流，因此畫出來的曲線在0附近變化很陡峭，比MOS陡峭很多(因為BJT公式是exponential，MOS是二次式)，也就是當我們輸入小信號時，會有很大的增益啦。不過這個小信號要控制在$\dfrac {V_{T}}{2}$以下喔，不然會跑出這條陡峭曲線的線性範圍，於是就失真了。

![Large signals](https://i.imgur.com/vovqieO.png)

接下來我們就真的來看看小信號輸入會變怎樣，我們將剛剛那個很醜的exponential公式做近似(因為${v_{id}}$很小)，結果得到：$i_{C1} = \dfrac {\alpha I}{2} - \dfrac {\alpha I}{2V_T}\dfrac {v_{id}}{2}$
，其實這就跟MOS的公式87%像：$i_{D1} = \dfrac {I}{2} - \dfrac {I}{V_{OV}}\dfrac {v_{id}}{2}$，可以看到BJT除了比MOS乘上$\alpha$倍以外，等於是用了$V_T$去取代$V_{OV}/2$。而如果不看共有的電流，只看增加的電流(也就是小信號)，得到$i_{c} = \dfrac {\alpha I}{2V_T}\dfrac {v_{id}}{2}$，剛好$g_m = I_C/V_T$，因此$i_{c} = g_m\dfrac {v_{id}}{2}$。

其實我們還有一個更有用的看法，先假設$R_{EE} \gg r_e$，因此$v_{id}$就是跨在$2r_e$上，因此$i_c = \alpha i_e = \frac{\alpha v_{id}}{2r_e}$，其中$r_e = V_T/I_E = \frac{V_T}{I/2}$，可得$i_c = \alpha i_e = \frac{\alpha v_{id}}{2r_e} = g_m \frac{v_{id}}{2}$，是不是跟剛剛一模一樣呢?

![small signals](https://i.imgur.com/A1my9u6.png)

剛剛導出的這個結果，在我們加上Degenerator電阻時非常好用，因為就只是把$r_e$變成$r_e+R_E$，所以$i_{e} = \dfrac {v_{id}}{2r_e+2R_e}$。

原本在MOS中，輸入電阻是無限大，但現在因為是BJT，所以我們要考慮它輸入differential信號時的電阻$R_{id}$，$R_{id}$基本上就是兩個$r_{e}$並聯，但因為在$r_{e}$那一端電流是input端的$\beta +1$倍，所以$R_{id}=(\beta +1)2r_{e}$，同樣地，如果有加上Degenerator電阻，則$R_{id}=(\beta +1)(2r_{e}+2R_{E})$。

再來我們來看BJT的differential增益，雖然剛剛我們說BJT在Collector端的電流只有$\alpha$倍，不過當我們表示增益時會用$g_m$，而$\alpha$已經被包括在$g_m$裡面了，所以增益仍然是$A_d = g_mR_C$(MOS是$A_d = g_mR_D$)。不過，如果有加上Degenerator電阻，就被打回原形了，要表示成$A_{d} = \dfrac {\alpha (2R_{C})}{2r_e+2R_e} = \dfrac {\alpha R_{C}}{r_e+R_e}$，其實就是output電阻除以input電阻後乘上$\alpha$倍啦。而BJT也有mismatch的狀況，跟MOS一樣就不再多談，但這邊我們只先講$R_C$的mismatch，$\Delta R_C$會造成common mode信號的增益是 $A_{cm} \approx \frac{R_C}{2R_{EE}}(\frac{\Delta R_C}{R_C})$，而這對於CMRR的影響會使$CMRR = \frac{A_d}{A_{cm}} = \frac{2g_mR_{EE}}{\Delta R_C/R_C}$。

講了這麼多mismatch的狀況，讓我們感覺這個誤差值很難評估大小，因此我們打算做一件事：把這些誤差的來源全部算到input信號的頭上！也就是說我們把這些誤差當成是input信號有一個偏差值，然後就假裝所有的mismatch都是好的，只是input信號的問題。蛤？為什麼要這樣冤枉人家？因為這樣有一個很大的好處，就是算出這個input"假偏差"電壓之後，我們就可以知道，如果你想讓你的input信號有用，就一定要大於這個"假偏差"電壓，不然就會被這個"假偏差"電壓蓋過去喔。

因此，我們定義input offset voltage$V_{OS} = V_O/A_d$，對於MOS來說，針對$R_D$的偏差，我們算出$V_{OS} = (\frac {V_{OV}}{2})(\frac{\Delta R_D}{R_D})$；針對$(W/L)$的偏差，我們算出$V_{OS} = (\frac {V_{OV}}{2})(\frac{\Delta (W/L)}{(W/L)})$；針對$V_t$的偏差則會直接影響到$V_{OV}$，結果很簡單，我們算出$V_{OS} = \Delta V_t$。由於這三種因素是independent，total的$V_{OS}$可以估計成$V_{OS} = \sqrt {(\frac {V_{OV}}{2} \frac{\Delta R_D}{R_D})^2 + (\frac {V_{OV}}{2}\frac{\Delta (W/L)}{(W/L)})^2 + (\Delta V_t)^2}$。

再來，BJT也要重複一次，這次不囉嗦，先講結論：把所有的$\frac {V_{OV}}{2}$換成$V_T$。針對$R_C$的偏差，我們算出$V_{OS} = V_T(\frac{\Delta R_D}{R_D})$；針對$(W/L)$的偏差，我們則直接看接面面積所造成的比例電流$I_S$的偏差，可得$V_{OS} = V_T(\frac{\Delta I_S}{I_S})$；至於$V_t$？抱歉，BJT根本沒有$V_t$，MOS的$V_t$的偏差是會直接影響到$V_{OV}$，但BJT的$V_{OV}$都用$V_T$來取代了，而$V_{T}$基本上是只受溫度影響，所以不會有mismatch的狀況啦。

所以BJT total的$V_{OS}$可以估計成$V_{OS} = \sqrt {( V_T \frac{\Delta R_D}{R_D})^2 + (V_T \frac{\Delta (W/L)}{(W/L)})^2 }$。

其實BJT還有一項東西也會mismatch，不過前面都沒提到，也就是 $ \beta$，但 $ \beta$所影響的是電流喔，他的mismatch會造成差動放大器兩邊的偏壓電流不一樣大，跟input offset voltage一樣，我們用input offset current來看待他，我們算出input offset current$I_{OS}=I_B(\frac{\Delta \beta}{\beta})$

再來，其實我們放大器最後一級的output很多都是需要單端對地的輸出，因此這時候我們就只能用到differential amp的其中一端直接接出來當output了，不過這樣另外半邊的放大就沒有用了，真的很虧，聰明的工程師想到一招，我們把放大器沒用到的那一邊的電流，透過current mirror，等效地換個方向直接灌到要輸出的那一邊，對於common mode信號來說，因為灌進去的電流跟原本就存在的電流是同方向同大小的，如果匹配的話，就不會有任何電流輸出到下一級電路了；對於differential信號來說，因為灌進去的電流跟原本就存在的電流是相反方向同大小的，所以加起來就變成兩倍了，原本另外半邊用不到的電流就可以被利用乾淨了，很聰明吧。
![single ended](https://i.imgur.com/rajeRZN.png)

這種結構我們稱之為Active Load differential amp，現在我們又重新算一次$G_m$和$R_o$了，不過不緊張，算出來$G_m = g_m$，$R_o = r_{o2} \Vert r_{o4}$，結果非常直觀，因此$A_d = g_m(r_{o2} \Vert r_{o4})$。

現在common mode的增益就不是0了，我們想知道對於common mode的$G_m$值是多少，稱作$G_{mcm}$(在前一節是不需要討論這個的)，我們用類似半電路的方式來看他(如下圖右)，忽略電晶體上的小電阻$\frac{1}{g_m}$之後，整條電路上就只剩下$2R_{SS}$，因此$G_{mcm} = \frac {1}{2R_{SS}}$，看單一半電路的$R_O$為 $2R_{SS}+r_{o}+(g_mr_o)(2R_{SS})$。

![Active Load differential amp](https://i.imgur.com/uFNhnun.png)

知道這件事情之後，我們就可以來看上圖左邊的部分，把剛剛已經求出$G_{mcm}$的地方取代成等效電路(也就是圖的左下角右下角。現在我們要來求$A_{cm}$，由於左半電路的$v_{g3}$跟$d_1$是接在一起的，上方的電晶體$Q_3$就可以視為$\frac{1}{g_m}$的小電阻，可算出$v_{d1} = v_{g3} = - G_{mcm}v_{icm}(R_o \Vert r_{o3} \Vert 1/g_{m3})$，因此我們就有了$v_{gs4}$，於是就知道$i_4 = g_{m4}v_{g3} = - g_{m4}G_{mcm}v_{icm}(R_o \Vert r_{o3} \Vert 1/g_{m3})$，針對$v_o$那個節點，我們寫出所有流出那個節點的電流$G_{mcm}v_{icm}+i_4+\frac{v_o}{R_{o2}}+\frac{v_o}{r_{o4}} = 0$，會等於零。省略$R_{o2}$跟$R_{o1}$後，整理一下，我們得到$A_{cm} \equiv \frac{v_o}{v_{icm}} = -\frac{1}{2R_{SS}}\frac{r_{o4}}{1+g_{m3}r_{o3}} \approx -\frac{1}{2g_{m3}R_{SS}}$。

由此我們可再算出$CMRR = (g_mr_o)(g_mR_{SS})$。

BJT也要算喔，但是太累了，直接講結論：

![](https://i.imgur.com/wgL8rx4.png)

對differential信號而言，
$G_m = gm$  
$R_o = r_{o2} \Vert r_{o4}$  
$A_d = g_m(r_{o2} \Vert r_{o4})$  
跟MOS一模一樣。  

但對於common mode來說，
$R_{id} = 2r_{\pi}$ => MOS是無限大輸入阻抗，BJT不優  
$A_{cm} \approx -\frac {r_{o4}}{\beta_{3}R_{EE}}$  
$CMRR = \frac{1}{2} (\beta_{3}g_mR_{EE})$  
BJT的CMRR不是無限大的原因是"$\beta$ 不是無限大"，回憶一下MOS的CMRR不是無限大的原因則是因為"$r_o$不是無限大"，注意只要$R_{EE}$(BJT的)或是$R_{SS}$(MOS的)選得夠大，CMRR還是會很大的，因此我們可以使用Wilson current mirror等結構來當作偏壓的電壓源喔。



### Ch9: Frequency response (9.1 - 9.7.2) 

[Lecture Ch9 PDF](http://cc.ee.ntu.edu.tw/~lhlu/eecourses/Electronics2/Electronics_Ch9.pdf)
[Backup](https://www.dropbox.com/s/zf7bewethi8ex3y/Electronics_Ch9.pdf?dl=0)

這一章開始我們要納入頻率對電路的影響，也就是說我們前面學了那麼多，其實都是很不負責任地假裝頻率是處於midband，所以不需要考慮頻率問題。那麼頻率不再處於midband時，會產生甚麼問題咧?主要的問題就是，電路中有很多電容，有些是自己加上去的，有些是寄生的小電容，無法控制，在特定頻率以外就會看到他們所造成的效應。

![midband](https://i.imgur.com/v8LQRWI.png)

首先，這些電容分成兩種：1.大的，2.小的。大的通常是人自己加上去的，像是bypass capacitors、coupling capacitors，又因為電容所造成的阻抗公式為 $\frac{1}{sC}$，所以只有在頻率低的時候，他所造成的阻抗影響才會大，不然在正常頻率或是高頻他幾乎就是短路；那小的呢？小的通常是寄生電容，藏在電晶體結構裡面，我們管不著。同理，電容所造成的阻抗公式為 $\frac{1}{sC}$，因此在一般頻率或是低頻時，他所造成的阻抗非常大，幾乎就是斷路，只有在高頻時才會把它視為一個電阻。

先來看個大的電容的例子：

![MC_20](https://i.imgur.com/m7P5Is0.png)

圖中三個電容 $C_{C1}$、$C_{C2}$(coupling capacitor)、$C_{s}$(bypass capacitor) 都是人加上去的，如果你仔細去算這個電路的轉換函數，會得到：

待續...



### Ch10: Feedback (10.1-10.2, 10.7-10.10) 
待續
### Ch12: OPAMP circuits (12.1 -12.2)  
待續
