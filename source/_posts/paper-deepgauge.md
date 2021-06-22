---
title: '《論文筆記》DeepGauge: Multi-Granularity Testing Criteria for Deep Learning Systems'
url: 3425.html
id: 3425
categories:
  - 論文筆記
date: 2019-03-03 13:48:06
tags:
excerpt: 
---

# 論文資訊
- 標題：DeepGauge: Multi-Granularity Testing Criteria for Deep Learning Systems 
- 作者：Lei Ma, Felix Juefei-Xu, Fuyuan Zhang, Jiyuan Sun, Minhui Xue, Bo Li, Chunyang Chen, Ting Su, Li Li, Yang Liu, Jianjun Zhao, Yadong Wang 
- 會議/期刊：ASE 2018 
- 備註：ACM SIGSOFT Distinguished Paper Award 
- 連結：https://arxiv.org/abs/1803.07519   
- 這篇論文主要是提出了一種新型態的 Neural Network Coverage（神經網路覆蓋率），不同於 DeepXplore[1] 所提出的版本。

*   **Notation**
    *   Let N = {n1,n2, . . .} be a set of neurons of a DNN
    *   Let T = {x1, x2, . . .} be a set of test inputs
    *   Use ϕ(x,n) to denote a function that returns the output of a neuron n ∈ N under a given test input x ∈ T
    *   Let Li denote the set of neurons on the i-th layer (1 ≤ i ≤ l )
    *   For a neuron n, let highn and lown be its upper and lower boundary output values, respectively, on the value range of its activation function, where highn and lown are derived from the training dataset analysis.
*   Neuron-Level Coverage Criteria以神經元為單位計算覆蓋率
    1.  **Major function region coverage**
        *   在測試集的時候把所有的神經元的最高和最低輸出都抓出來。
    2.  K-multisection Neuron Coverage（KMNC）
        *   把每一個神經元的輸出最低到最高切出 k 個section
        *    k-multisection Neuron Coverage of a neuron n
            *   ![](https://imgur.com/RzdQdgZ.jpg)
        *   k-multisection Neuron Coverage of a DNN
            *   ![](https://imgur.com/HYUqTqZ.jpg)
    3.  Neuron Boundary Coverage（NBC）
        *   把負無限大到最低值 跟 最高值到無限大之間的輸出都歸類在這裡
        *   UpperCornerNeuron = {n ∈ N | ∃x ∈ T : ϕ(x,n) ∈ (highn, +∞)}
        *   LowerCornerNeuron = {n ∈ N | ∃x ∈ T : ϕ(x,n) ∈ (−∞, lown)}
        *   ![](https://imgur.com/zheL4xe.jpg)
    4.  Strong Neuron Activation Coverage
        *   只看 UpperCornerNeuron 的輸出
        *   ![](https://imgur.com/EJ8ynGq.jpg)
*   Layer-Level Coverage Criteria以階層為單位計算覆蓋率
    1.  Top-k Neuron Coverage
        *   每一層神經元輸出數值排出 Top-k，最後計算曾經變成 Top-k 的神經元數量，除以總神經元數量
        *   ![](https://imgur.com/1iNkdll.jpg)
    2.  Top-k Neuron Patterns
        *   把輸出 Top-k 的路徑記作一個 Pattern，計算總共有幾種 Pattern
        *   ![](https://imgur.com/xqKGH6j.jpg)

# Evaluation  

*   產生的樣本、DNN model 的參數跟覆蓋率的設定

[![](https://imgur.com/GDoPm0C.jpg)](https://imgur.com/GDoPm0C.jpg)
  
[![](https://imgur.com/fpEIluF.jpg)](https://imgur.com/fpEIluF.jpg)  

[![](https://imgur.com/UeLUv6H.jpg)](https://imgur.com/UeLUv6H.jpg) 

# Findings

1.  無論如何，Adv樣本都會觸及到邊界。
2.  增加覆蓋率不代表會找到問題。
3.  SNAC 比 NBC 高，可能是因為 ReLU 把負數砍成零，所以 Lower Bound 小很多。

## Remark 1

1.  對於 NBC 跟 SNC 來說，上下界設的越寬，覆蓋率增加越少（因為範圍被壓縮了）
2.  對於 TKNC 來說，K越大，覆蓋率增加越少
3.  對 TKNP 來說，K越大，Pattern 越多

## Remark 2

1.  對 NBC 跟 SNAC 來說，對抗例樣本會增加他們的覆蓋率，反過來說，觸及邊界的很有可能就是對抗例樣本。
2.  用 CW 攻擊產生的對抗例樣本對增加覆蓋率影響不大。

# Reference 
[1] Kexin Pei, Yinzhi Cao, Junfeng Yang, Suman Jana, “DeepXplore: Automated Whitebox Testing of Deep Learning Systems,” SOSP 2017.