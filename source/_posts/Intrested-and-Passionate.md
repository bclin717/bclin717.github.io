---
title: 做研究，僅僅有興趣夠嗎？
categories:
  - 日常筆記
date: 2019-11-24 00:20
tags:
excerpt: 
top_image: https://i.imgur.com/iC91sMD.png
---

# 前言

最近一直在思考一件事情，就是做研究，僅僅有興趣是足夠的嗎？
會思考這件事情，得從為什麼我選擇做神經網路的測試方法說起。

# 我的夢想

之前談 MIT 的時候，我曾說過我的夢想是當一名科學家。可能是科幻作品的影響，我從小就想做一些有「智慧」的東西。
《Portal》裡的 GLaDOS、《2001太空漫遊》的 Hal 9000、《Pluto》裡的未來機器人世界令我深深著迷。

但很早我就認清了事實並放棄這個夢想，因為至少半世紀內，這種 Artificial Intelligence (AI) 不太可能實現。原因很簡單，要製作出真正的「智慧」，不僅僅需要電腦科學領域的努力，還得仰賴諸如哲學、物理學、神經科學、腦科學、生化、生科等許多領域的極大突破。

Deep Learning 大放異彩之後，我心中燃起一線希望，這項讓電腦在圍棋上打敗人類的技術，引起我極大的興趣。雖然知道現階段的應用還離夢想很遠，但我還是去學習了相關領域的知識。

在做研究的過程中，寫程式、寫算式、訓練模型確實是很有趣，但這個 Deep Neural Network 顯然不是我要的。會意識到這件事情，還是跟女友在聊書的時候發現的。我發現我其實對腦科學和神經科學很有興趣，也常買相關的書，但就只是興趣而已，跟對 Deep Learning 的興趣是一樣的。我沒辦法再做更多。

# 做研究

若要談論興趣，在 Computer Science 的領域中，我對於 Security 的興趣還是比較濃厚的，從小也對做一名 Hacker 有深深的嚮往。

在這幾個月做研究的過程中，我發現，對於做神經網路的研究，我居然是這麼沒有熱忱。雖然寫程式的時候覺得很好玩，但對其他的一切卻很麻木，沒有感覺，甚至有些厭煩。在寫這篇論文前，Wesley 要我思考做研究的過程中，最喜歡的和最不喜歡的，並且評價自己適不適合做研究。其實做研究的難點還是在保持初心，做著做著容易忘記自己在做什麼。對於神經網路，我缺乏最後一步的熱忱，讓我興奮的，都是思索如何能把測試和安全用在神經網路上，而不是思索神經網路缺陷的本質。

於是我發現，原來一樣是興趣，也是有分高低的。要做研究，僅僅是有興趣是不行的，還必須有熱忱。

當初會選擇做這個題目，也是希望把 Cyber Security 跟 AI 這兩個興趣做結合，結果到了年中，我開始想把重心轉往 Fuzz Testing，做程式分析和測試顯然更加乏味卻也更加有趣。不過，這個題目確實是很新穎的，指導教授興奮，我也興奮，所以還是繼續做下去，畢竟對我來說，寫這些程式並不需要花多少時間和心力。


# 軟體測試與神經網路

結果這個研究領域才過一兩年就飽和了。
看了近兩年關於 Testing Neural Networks 的論文，基本上都沒有太大的變化，實用性也都還很低。看了這麼多，其實除了 Pei et al.[1] 的 DeepXplore、Tian et al.[2] 的 DeepTest 跟 Katz et al.[3] 的 Reluplex 是真的把測試和驗證帶入神經網路的領域，大多只是把傳統軟體的那套搬來用，蹭熱度趕快刷刷論文而已，少有觸及到神經網路本質之作。

比如 Gopinath et al.[4], Sun et al.[5] 就是把 Symbolic 跟 Concolic 的概念套上去，而 Guo et al.[6]、Ma et al.[7] 還有 Xie et al.[8] 都是從傳統 Fuzzing 的角度出發，提出 Coverage 的概念，並把 Coverage-Guided Fuzzing 那套拿來用。方向是正確的，但實用性其實沒那麼高。畢竟你一樣拿出 FGSM，epsilon 調到超高，Misclassification Rate 大概也接近 100% 了，哪裡還需要什麼 Coverage-Guided 呢？

我們做自動化測試、模糊測試就是為了要找到軟體的問題，要能夠 Validate 和 Verify 軟體，但 Deep Learning 就像上個世紀剛發展的軟體產業一般，漏洞百出。
試想，上個世紀末 Barton Miller 教授提出 Fuzz Testing 的概念時，那些 Unix 的軟體不也是漏洞百出，只要用一些隨機字串就能使重要的 API 或程式崩潰。三十年後，即使是廣為人知的 AFL 以及其延伸出的 Fuzzer 都未必能輕易地找到程式的漏洞。

可以說，我認為軟體測試和軟體的發展是相輔相成的，在神經網路還未成熟以前，軟體測試很快便走到了瓶頸。體認到這個事實的我，還是回去做資安好了。況且，人的一生要做好一件事情就已經很吃力了。

# 結語

做了一次研究，發現又更了解了自己一點。如同 Wesley 所說，做學術研究，有很大一部分是對自己的內宇宙進行挖掘與探索。
它讓我知道，僅僅有興趣是不足以做研究的，若沒有激情和熱忱，以及對於自己充分的了解，研究不過是譁眾取寵，或是痛苦的深淵罷了，終究只能產出一堆廢紙。


# Reference

[1] Kexin Pei, Yinzhi Cao, Junfeng Yang and Suman Jana, "DeepXplore: Automated Whitebox Testing of Deep Learning Systems," ACM Symposium on Operating Systems Principles (SOSP) 2017.
[2] Yuchi Tian, Kexin Pei, Suman Jana and Baishakhi Ray, "DeepTest: Automated Testing of Deep-Neural-Network-driven Autonomous Cars," ACM/IEEE International Conference on Software Engineering (ICSE) 2018.
[3] Guy Katz, Clark Barrett, David Dill, Kyle Julian and Mykel Kochenderfer, "Reluplex: An Efficient SMT Solver for VerifyingDeep Neural Networks," Computer Aided Verification (CAV) 2017.
[4] Divya Gopinath, Kaiyuan Wang, Mengshi Zhang, Corina S. Pasareanu and Sarfraz Khurshid, "Symbolic Execution for Deep Neural Networks,"  	arXiv:1807.10439 [cs.SE].
[5] Youcheng Sun, Xiaowei Huang, Daniel Kroening, James Sharp, Matthew Hill and Rob Ashmore, "DeepConcolic: Testing and Debugging Deep Neural Networks," International Conference on Software Engineering (ICSE) 2019.
[6] Jianmin Guo, Yu Jiang, Yue Zhao, Quan Chen and Jiaguang Sun, "DLFuzz: Differential Fuzzing Testing of Deep Learning Systems," ESEC/FSE'2018.
[7] Lei Ma, Felix Juefei-Xu, Fuyuan Zhang, Jiyuan Sun, Minhui Xue, Bo Li, Chunyang Chen, Ting Su, Li Li, Yang Liu, Jianjun Zhao and Yadong Wang, "DeepGauge: Multi-Granularity Testing Criteria for Deep Learning Systems," IEEE/ACM International Conference on Automated Software Engineering (ASE 2018).
[8] Xiaofei Xie, Lei Ma, Felix Juefei-Xu, Hongxu Chen, Minhui Xue, Bo Li, Yang Liu, Jianjun Zhao, Jianxiong Yin and Simon See, "DeepHunter: Hunting Deep Neural Network Defects via Coverage-Guided Fuzzing," arXiv:1809.01266 [cs.SE].