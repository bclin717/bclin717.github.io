---
title: >-
  《論文筆記》DeepHunter: Hunting Deep Neural Network Defects via Coverage-Guided
  Fuzzing
url: 3539.html
id: 3539
categories:
  - 論文筆記
date: 2019-03-18 19:30:08
tags:
---
# 論文資訊
- 標題：DeepHunter: Hunting Deep Neural Network Defects via Coverage-Guided Fuzzing 
- 作者：Xiaofei Xie, Lei Ma, Felix Juefei-Xu, Hongxu Chen, Minhui Xue, Bo Li, Yang Liu, Jianjun Zhao, Jianxiong Yin, Simon See 
- 機構：Nanyang Technological University、Harbin Institute of Technology、Carnegie Mellon University、University of Illinois at Urbana–Champaign、Kyushu University、NVIDIA AI Technology Center 
- 會議/期刊：None 連結：https://arxiv.org/abs/1809.01266   
<!-- more -->
在傳統軟體測試領域，我們很常用一些自動化測試來產生測資並進行測試，進而找出漏洞，最後產生報告供測試人員分析。其中有一種測試方式叫做 Fuzz Testing（Fuzzing），意思就是模糊測試。這篇論文便是以同樣的概念對深度神經網路進行測試，由於作者同時也是 DeepGauge 的研究群，他們引用了自己在 DeepGauge 中提出的神經網路覆蓋率指標，在這邊論文提出了 Coverage-Guided Fuzzing，以程式碼覆蓋率為 feedback 去有效率地產生大量模糊測試的測資。這在傳統軟體測試領域是很常見的策略，像是 AFL、libFuzxer 及 VUzzer 就是 coverage based 的 Fuzzer。 
基本上，一個 CGF 應該要進行有以下的 Loop：
1.  從 Seed pool 裡面選擇 seed
2.  利用特定修改策略（像是bitwise/bytewise flip），多次修改種子去產測資
3.  用產出來的測資去跑目標程式，紀錄執行路徑
4.  回報產生錯誤的種子，把那些覆蓋到不同路徑的種子存進 seed pool（提升覆蓋率的種子）

重點有兩個：修改策略、覆蓋率，前者注重是如何產生新的、有效率的種子，後者則是評斷種子優劣的標準。
這是這篇論文提出的 DeepHunter 的流程圖。
 [![](https://i.imgur.com/XKDG2DM.jpg)](https://i.imgur.com/XKDG2DM.jpg) 
 
文中定義一個 atomic 輸入為一個種子，一個 batch 指的則是一堆圖片。為什麼是用 Batch Pool，是因為作者認為一次修改一定數量的種子，然後讓 DNN 一次去做辨識會比較有效率。另外，為了把有「潛力」 的種子往前推，會進行一個 Power scheduling 的動作；為了避免製作出沒有意義的種子，會在修改後進行 Sanity checking。最後進行完 Coverage Analysis 後，會做一個 Batch Prioritization 的動作讓很少被選到修改的 Batch 優先權往前提升。 

[![](https://imgur.com/RR6SBtO.jpg)](https://imgur.com/RR6SBtO.jpg) 

# 3.2 轉換與修改（Transformation and Mutation）
傳統 fuzzer 像是 AFL 用的策略產出的測資，對於 DNN 來說會是沒有意義的；而在改動大小和產出有意義測資之間要取得平衡是很難。 所以作者想出了一種圖像變換的策略，稱為 metamorphic mutation strategy，意思就是說，我改動你這個種子，但產生出來的測資的 semantic（語意）必須和原來是一樣的。 對於影像進行變化的方法被分為兩種：

*   Pixel Value transformation P
    *   對比度
    *   量度
    *   模糊
    *   雜訊
*   Affine transformation G
    *   影像翻譯
    *   影像縮放
    *   影像斜變
    *   影像旋轉

為了避免產出無意義的測資，Affine transformation 只能做一次；Pixel Value transformation 則可以做多次，但會計算 L0 和 L∞ 來限制改變大小。
假定 I 透過 pixel value transformation 被改成 I'，那我們說在 f(I,I') 成立時 I' 是 有意義的。 

[![](https://i.imgur.com/31qN9LO.jpg)](https://i.imgur.com/31qN9LO.jpg) 

L0 指的是 pixel 最大改變量，size(I) 則是圖片的 pixel 數量。作者假定如果改動的 pixel 很小，小於 a x size(I) 時語意不受影響。萬一超過了，那只要不要改超過 b x 255 就好了。 

[![](https://imgur.com/h2kIbh2.jpg)](https://imgur.com/h2kIbh2.jpg)

[![](https://imgur.com/gIVr9zN.jpg)](https://imgur.com/gIVr9zN.jpg) 
Algorithm 2 是修改的過程，I 是 Input 而 I' 是 output。state 是一個 bool，用來記錄 Affine Transformation 是否被使用過了。作者表示他們想在產生有意義的測資時，盡可能最大化修改的次數，也就是 TRY_NUM。如果 state 目前是 0，就隨機挑選一種修改方式，如果是 1，那就挑 Pixel Value Transformation，參數則是隨機。

# 3.3 Power Scheduling 
用mutation potential來計算可以修改的空間。公式是 B x 255 x size(I) - sum(abs(I-I'0)) mutation potential 越大，代表越接近種子，而且會被更優先修改。  

# 3.4 Plugable Coverage-Guided Fuzzing 

[![](https://imgur.com/7QvOHVU.jpg)](https://imgur.com/7QvOHVU.jpg) 
DeepHunter 用了六種不同的 coverage criteria 去看哪一個 batch 要被留著。但是如果一直收 coverage，程式會不堪負荷，為了加速，會把一些得到差不多輸出的 seed 忽略掉，不必特別保留 coverage 的值。並用了 bucket 的機制來避免 trace exploitation issue，跟傳統 fuzzing framework 很像。 

# 3.5 Batch Prioritization 調整每一個 Batch 被挑選到的機率，機率的公式是
[![](https://imgur.com/RWrlq0O.jpg)](https://imgur.com/RWrlq0O.jpg) 
避免總是有趣的 Batch 被挑選，其他 Batch 依然有 Pmin 作為最低機率。