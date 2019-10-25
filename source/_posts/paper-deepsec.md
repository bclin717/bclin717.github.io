---
title: '《論文筆記》DEEPSEC: A Uniform Platform for Security Analysis of Deep Learning Model'
url: 3427.html
id: 3427
categories:
  - 論文筆記
date: 2019-02-19 12:09:11
tags:
---

# 論文資訊
- 標題：DEEPSEC: A Uniform Platform for Security Analysis of Deep Learning Model 
- 作者：Xiang Ling, Shouling Ji, Jiaxu Zou, Jiannan Wang, Chunming Wu, Bo Li, Ting Wang 
- 研究機構：Zhejiang University, Alibaba-Zhejiang University Joint Research Institute of Frontier Technologies, UIUC, Lehigh University 
- 會議/期刊：IEEE S&P 2019 
- 連結：[點我](https://nesa.zju.edu.cn/download/DEEPSEC%20A%20Uniform%20Platform%20for%20Security%20Analysis%20of%20Deep%20Learning%20Model.pdf)   
- 
- 
# Introduction
對於人工智慧界的軟體工程現況，作者提出了幾個問題，第一、即目前對於評估深度學習模型品質的指標過於簡單。例如，誤判率（Misclassification Rate）並無法用來評估一個攻擊方法。 
第二、之前的論文總是評估一小部分的攻擊或防禦方法。例如，許多防禦方法都只用一些強的攻擊，但其實很多時候能防禦強的攻擊不代表對於弱的攻擊免疫。
第三、攻防的快速競爭使得許多方法快速失效。例如，許多防禦方法所採用的 Gradient Obfuscation Strategy 已經沒什麼用了[1]。 

這些因素都會讓研究的結論產生矛盾，像是 Defensive Distillation[2] 原來是用來抵擋 JSMA 攻擊，但現在我們發現其實只對新攻擊的 Marginal Robustness 有增強而已，而且後來的研究指出用上 DD 方法所訓練出的模型表現會比原來的模型還要差[3]。所以說作者認為我們必須要有一套可以泛用，並且提供許多資訊以供評估 Adversarial 攻擊與防禦。而這個平台必須有四個特點： 
- Uniform - 這平台可以在相同設定環境下比較不同攻擊與防禦方法 
- Comprehensive - 可以提供所有代表性的攻擊與防禦方法 
- Informative - 可以給出非常多的指標來評估攻擊與防禦方法 
- Extensible - 可以簡單地增加新的攻擊與防禦方法

而目前有的平台，如 Cleverhans（[Github](https://github.com/tensorflow/cleverhans)）都沒有達到以上的所有需求。而這篇論文所提出的 DEEPSEC 則包括了 16 種攻擊、10 種攻擊指標、13 種防禦以及 5 種防禦指標。   

# Attacks & Defenses

關於這篇論文所提出的實驗，都是以白箱攻擊為前提，攻擊者知道所有關於目標模型的細節（深度、節點數、訓練集...之類的），但不知道所部屬的防禦方法。Table 1 給出了所有在這篇論文使用到的攻防方法、評估指標以及其縮寫。 
[![](https://imgur.com/DuLKjVN.jpg)](https://imgur.com/DuLKjVN.jpg)   

## A. 攻擊方法 這篇論文將攻擊方法分為UA（Un-targeted Attacks）跟TA（Targeted Attacks），還有 Non-iterative Attack 與 Iterative Attack。所以總共有四種攻擊。以上不做敘述。 
## B. 攻擊的評測指標
1.  Misclassification（誤判）
    1.  Misclassification Raito（MR 誤判率）：即對抗樣本使分類器誤判的百分比值
    2.  Average Confidence of Adversarial Class（ACAC 平均對抗樣本信心值）：即對抗樣本被成功誤判後的信心值（誤判的確信程度有多高）。
    3.  Average Confidence of True Class（ACTC 平均正確分類信心值）：即對抗樣本的原正確分類之信心值（即離正確分類的距離）。
2.  Imperceptibility（人類無法感知）
    1.  Average Lp Distortion（ALDp 平均 Lp 扭曲度）：即所有 Lp norm distance （p = 0, 1, infinity）的加總平均值。
    2.  Average Structural Similarity（ASS 平均結構相似性）：即平均對抗樣本與原樣本的 SSIM index 值（SSIM 是一種用以衡量兩張數位影像相似程度的指標）。
    3.  Perturbation Sensitivity Distance（PSD 擾動感知距離）：根據 Contrast Masking Theory [4] 提出的用來衡量人眼對於擾動感知程度的指標 [5]。
3.  Robustness（強健 / 魯棒性）
    1.  Noise Tolerance Estimation（NTE 雜訊容忍預估）：在[5]這篇論文中，一個對抗樣本的強健性是由 Noise tolerance 預估的，反映了一個對抗樣本可以忍受多大的雜訊而不被破壞。
    2.  Robustness to Gaussian Blur（RGB 高斯模糊耐性）：對抗性樣本可以容忍高斯模糊後處理的比值。
    3.  Robustness to Image Compression（RIC 影像壓縮耐性）：對抗性樣本可以容忍影像壓縮處理的比值。
4.  Computation Cost（CC 計算成本）：即攻擊者產生對抗例樣本的平均時間。

## C. 防禦方法

1.  Adversarial Training：即拿對抗例去訓練模型，藉此讓模型對對抗例免疫。
2.  Gradient Masking/Regularization：減少模型對對抗例的敏感度，並且隱藏梯度。
3.  Input Transformation：將 input 樣本做轉換，以破壞惡意擾動。
4.  Region-based Classification：[6]（看不太懂）
5.  Detection-only Defenses：把對抗例分類對實在太難了，我們只要可以「檢測」出對抗例，並且拒絕接收就好。

## D. 防禦的評測指標：防禦可以從兩個角度來評估，「功能保留」以及「抵抗攻擊」。

1.  Classification Accuracy Variance（CAV 分類精準度變異量）：增強防禦後的模型分類一般測試集的精準度與原模型的差異值。
2.  Classification Rectify/Sacrifice Ratio（CRR/CSR 分類矯正比/分類犧牲比）：增強防禦後的模型分類一般測試集的時候，原來分錯變成分對的就是 CRR；分對變成分錯的就是 CSR。
3.  Classification Confidence Variance（CCV 分類信心值變異量）：增強防禦後的模型可能不會影響精準度，但信心值會有所波動，CCV 就是原來的信心值和增強防禦後的信心值之平均比值。
4.  Classification Output Stability（COS 分類輸出穩定值）：利用 JS divergence[7] 去計算輸出機率的相似度。

# System Design And Implementation

![](https://imgur.com/WrjeYsN.jpg)

## A. System Design：分為五個部分

1.  Attack Module（AM）：製作對抗例去攻擊深度學習模型，共 16 種攻擊，一半是 UA，一半 TA。
2.  Defense Module（DM）：防禦深度學習模型，共 13 種防禦。
3.  Attack Utility Evaluation（AUE）：10 種評估攻擊的指標。
4.  Defense Utility Evaluation（DUE）：5 種評估防禦的指標
5.  Security Evaluation（SE）：平衡 AM 和 DM 的安全性指標，可以讓使用者知道這個模型抵抗對抗例的能力如何。

## B. System Implementation

# Evaluations

平台： Intel Xeon 2.2GHz CPU x 2 256GB RAM GTX 1080 x 1 

## A. Evaluation of Attacks

1.  Experimental Setup
    1.  Datasets：MNIST / CIFAR-10
    2.  Mode：7-layer CNN / ResNet-20
    3.  Accuracy：99.27% / 85.95%
    4.  首先挑了 1000 個可以被正確分類的 sample，然後針對每一種攻擊都生成 1000 張對抗例，再用評估方法對所有攻擊做評估。TA 的 Class 則是隨機選擇但是統一的。
    5.  參數部分都統一。
2.  Experiemntal Results

![](https://imgur.com/pfGeNsO.jpg)

1.  Misclassification：Iterative 強於 Non-iterative
    *   Remark 1：大部分狀況下，目前的所有攻擊都有非常高的成功率（以 MR 來看的話，Iterative 的所有攻擊方式，在 CIFAR-10 上都有接近 100% 的 MR），都能很有效的誤導目標模型。有個有趣的現象是，如果對抗例的 ACTC 比較低，那對於攻擊不同模型時較具有泛用性。
    *   即存在一種狀況，若分類後 真正的 class 信心值很低，其他 class 的信心值無論高低，那這個泛用性就很高；若真正的 class 信心值很高，假 class 只是相對低一點，那泛用性就不高。直接比較 ACAC 不準，因為參數不同，例如 ILLC （L無限攻擊）跟 CW2（L2 攻擊）攻擊是不同的，要看 ACTC 的值。
    *   例子：FGSM 跟 OM 產生的對抗例在 CIFAR-10 都有 0.75 的 ACAC，但是 FGSM 的 ACTC 卻是 OM 的六倍低，這代表 FGSM 的泛用性會比 OM 更高。
2.  Imperceptibility：PSD 很敏感，但是量測 L2 攻擊產生的對抗例時，數值都比 L1 和 L無限 小很多。
    *   Remark 2：在所有指標中，PSD 是對對抗例擾動的 Imperceptibility 最敏感的，ASS 則相反，是最不敏感的，所以不建議用來量化對伉例。在這一個測試裡面也發現誤判率和 Imperceptibility 是有確定的 trade-off 關係的。
3.  Robustness：利用了 Guetzli 去產生高品質的 jpg 壓縮檔，RGB 指標用的樣本，高斯模糊的 radius 是 0.5；RIC 指標用的則是設為 90% 的壓縮率。測試後發現， NTE 比 RGB 跟 RIC 更有意義。可能是因為越高的 NTE 表示了容忍的雜訊越多，根據 Imperceptibility 的結論，雜訊增加意味著誤判機率越高，所以比低 NTE 更能忍受影像轉換。另一方面， NTE、RGB、RIC 之間的關係是非線性的。
    1.  ACAC 越高，RIC 跟 RGB 就越高。是因為 ACAC 可以影響 NTE，而間接影響了 RGB 跟 RIC。
    2.  Remark 3：對抗例的強健性是由 ACAC 影響的。另外 UA 比 TA 強健性更高。
4.  Computation Cost：設定保持不變，計算了各種攻擊方法的平均對抗例產生時間，iterative 通常比 non-iterative 慢很多，多花了超過十倍以上的時間。

## B. Evaluation of Defenses

1.  Experimental Setup
2.  Results

![](https://imgur.com/IQ7yaux.jpg)

*   NAT、DD、TE、RC 在 MNIST 跟 CIFAR-10 上面的精準度並沒有受到什麼影響。
*   IGR-、RT-、和 PD 強化過的模型的精準度則在 CIFAR-10 上受到很大的影響。
*   如果準確率下降，CSR 多數大於 CRR。
*   部屬防禦後 CCV 都會受到影響，如 PAT 和 IGR 則特別嚴重。這是因為部屬後的模型變得不穩定。
*   COS 跟 CAV 的變化傾向是相同的。
*   Remark 4：大部分指標都還滿有效的。

## C. Defenses vs. Attacks

1.  Complete Defenses：將對抗例正確辨識出來。![](https://imgur.com/ypSORtO.jpg)
    *   Results
        *   大部分防禦方法都有用，但有大有小。像是 NAT 對所有攻擊都有 80% 以上的抵擋能力，而所有方法平均是 58.4%。
        *   抵擋 TA 比 UA 還有用。作者猜測是因為 UA 只是讓模型誤判，泛用性比較強；而 TA 則通常是針對模型去做攻擊，所以用了防禦後模型不同，效果就會打折扣。
    *   Remark 5：多數防禦方法都有比較適合的攻擊方法，而通常有 retrain 模型的比沒有的強。
2.  Detection：偵測出對抗例並拒絕輸出。![](https://imgur.com/GlwxLTR.jpg)
    *   Experiemental Setup
        *   先找了 4-A 成功被辨識錯誤的對抗例
        *   再從測試集裡面隨機挑選可以被正確辨識的測試樣本
    *   Results
        *   AUC：當你隨機挑選一個正樣本以及一個負樣本，當前的分類算法根據計算得到的Score值將這個正樣本排在負樣本前面的概率就是AUC值。
        *   所有偵測方法的效果都不錯，平均 AUC 都有高於 70% 的表現。
        *   LID 中的 AUC 平均表現最好，但再面對 DF 或 OM 都只有 65%，比 FS 和 MagNet 還低（平均高於 80%）。
        *   LID 的 TPR 表現最好，但是 FS 和 MagNet 在 MNIST 表現比較好，只是在 CIFAR-10 比較差。可能是因為參數調整的問題。
        *   比較高的擾動不一定比較容易被偵測到。
    *   Remark 6：所有的偵測方法對目前的攻擊方法都有一定效果。不同的偵測方法有適合他們的攻擊方法。而高的擾動不一定比較容易被偵測。

# Case Studies

## A. Case Study 1: Transferability of Adversarial Attacks 

![](https://imgur.com/sDZPYF2.jpg) 

主要為了測試不同攻擊的遷移性。

1.  Experimental Setup：訓練了三個模型要來進行測試
    *   Model 1：一樣的模型，但經歷了不同的隨機初始化。
    *   Model 2：一樣的模型設定，但是神經網路結構有些微不同。
    *   Model 3：完全不同的模型。
2.  Results
    *   個種攻擊方法或多或少都有一定的遷移性
    *   對抗例在其他模型的 ACAC 高於原模型，作者猜測原因是原來信心值很低的對抗例會遷移失敗，而遷移成功的通常是信心值很高的對抗例。
    *   UA 的遷移性高於 TA，在 CIFAR-10 測試集，UA 平均有 74.6%，TA 僅僅 10%。
    *   L無限 攻擊遷移性強過 L2 或 L0。
    *   Remark 7：不同的攻擊有不同的遷移性
        *   UA 遷移性強過 TA
        *   L無限 遷移性強過其他攻擊
        *   對抗例在其他模型的 ACAC 高於原模型

## B. Case Study 2: Is Ensemble of Defense More Robust 

![](https://imgur.com/mcfXM9w.jpg) 

不同防禦手法組合後抵禦攻擊的能力是否會增強？

1.  Experiment Setup：和 4A 用的測試方法一樣。並有三種防禦組合：
    *   Completely-random Ensemble：隨機從九種防禦中選擇三種組合
    *   Interclass-random Ensemble：從三種不同類別的防禦方法中各隨機挑選一種進行組合
    *   Best-defense Ensemble：從前面的 4-C1 測試中表現最好的前三名進行組合，對 MNIST 用 PAT、TE、NAT；對 CIFAR-10 則是 NAT、EIT、EAT。
2.  Results
    *   Remark 8：把防禦方法都組合起來不會比較有效，但可以增加 lower bound。這部分跟之前看過的[8]一樣。

# Reference 

[1] A. Athalye, N. Carlini, and D. Wagner, “Obfuscated Gradients Give a False Sense of Security: Circumventing Defenses to Adversarial Examples,” in ICML, 2018. 
[2] N. Papernot, P. McDaniel, X. Wu, S. Jha, and A. Swami, “Distillation as a defense to adversarial perturbations against deep neural networks,” in S&P, 2016. 
[3] A. S. Ross and F. Doshi-Velez, “Improving the adversarial robustness and interpretability of deep neural networks by regularizing their input gradients,” in AAAI, 2018. 
[4] A. Liu, W. Lin, M. Paul, C. Deng, and F. Zhang, “Just noticeable difference for images with decomposition model for separating edge and textured regions,” IEEE Transactions on Circuits and Systems for Video Technology, 2010. 
[5] B. Luo, Y. Liu, L. Wei, and Q. Xu, “Towards imperceptible and robust adversarial example attacks against neural networks,” in AAAI, 2018. 
[6] X. Cao and N. Z. Gong, “Mitigating evasion attacks to deep neural networks via region-based classification,” in ACSAC, 2017. 
[7] Wikipedia, “Jensen–shannon divergence,” https://en.wikipedia.org/wiki/Jensen-Shannon divergence. 
[8] W. He, J. Wei, X. Chen, N. Carlini, and D. Song, “Adversarial example defense: Ensembles of weak defenses are not strong,” in WOOT, 2017.