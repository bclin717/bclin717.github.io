---
title: MIFARE Classic 二代悠遊卡破解 筆記
url: 2442.html
id: 2442
categories:
  - 硬體研討
  - 軟體研討
  - 預設
date: 2016-04-29 15:28:31
tags:
---

一、介紹：

1.  記憶體結構：
    1.  MIFARE Classic 卡的記憶體晶片被分成 16 個 Sector（64 Byte），每個 Sector 被切成 4 個 Block（16 Byte）。
    2.  第一個 Sector 的第一個 Block 用來放置該卡片的 UID、製造商...等卡片資料。
    3.  每個 Sector 的最後一個 Block 用來放置該 Sector 的兩組 Key，以及存取條件（Access Conditions） \[caption id="attachment_2444" align="alignnone" width="547"\]![US20110016275A1-20110120-D00000](http://iga.candy-sky.net/wp-content/uploads/2016/04/US20110016275A1-20110120-D00000-1053x1080.png) 內部記憶體結構\[/caption\] 
2.  讀卡（授權與初始化）過程：
    1.  由於有防碰撞（Anti-Collision）機制，讀卡機會先選擇卡片，並讓卡片將其 UID 傳送給讀卡機。
    2.  讀卡機請求卡片授權存取某 Block：b。
    3.  卡片傳送一組Nonce（隨機數）給讀卡機。從這時起，通訊過程皆被加密。
    4.  讀卡機也產生一組 Nonce，並計算出卡片 Nonce 的答案，並將兩者加密後傳回卡片。
    5.  卡片驗證答案之後，計算出讀卡機 Nonce 的答案，傳回讀卡機。
    6.  授權完畢。 \[caption id="attachment_2451" align="alignnone" width="473"\]![授權過程](http://iga.candy-sky.net/wp-content/uploads/2016/04/2016-04-28_190427.jpg) 授權過程\[/caption\] 
3.  加密：
    
    1.  利用內部 Crypto-1 產生 Key Stream，加密即與 KS 進行 XOR。
    2.  Crypto-1 初始狀態設定：
        1.  先放入 Sector Key。
        2.  UID 與 Nonce 進行 XOR，再與 Feedback Function L 產生的 Bit 進行 XOR，放入 Crypto-1 最右邊位元，全部往左 Shift 一格。
        3.  Feedback Function L（X0X1 . . . X47）:= X0 ⊕ X5 ⊕ X9 ⊕ X10 ⊕ X12 ⊕ X14⊕ X15 ⊕ X17 ⊕ X19 ⊕ X24 ⊕ X25 ⊕ X27 ⊕ X29 ⊕ X35 ⊕ X39 ⊕ X41 ⊕ X42 ⊕ X43。
    3.  產生 Key Stream：
        1.  利用 Filter Function 產生 KS： \[caption id="attachment_2461" align="alignnone" width="545"\]![Key Stream](http://iga.candy-sky.net/wp-content/uploads/2016/04/2016-04-28_201854.jpg) Key Stream\[/caption\]
        2.  Filter Function：
            1.  fa（y0, y1, y2, y3） := （（y0 ∨ y1）⊕（y0 ∧y3））⊕（y2 ∧（（y0 ⊕ y1）∨ y3））。
            2.  fb（y0, y1, y2, y3） := （（y0 ∧ y1）∨ y2）⊕（（y0 ⊕ y1）∧（y2 ∨ y3））。
            3.  即 fc（y0, y1, y2, y3, y4） := （y0 ∨ （（y1 ∨ y4） ∧ （y3 ⊕ y4））） ⊕（（y0 ⊕ （y1 ∧ y3）） ∧ （（y2 ⊕ y3） ∨ （y1 ∧ y4）））。 \[caption id="attachment_2457" align="alignnone" width="828"\]![Crypto-1 結構](http://iga.candy-sky.net/wp-content/uploads/2016/04/2016-04-28_200804.jpg) Crypto-1 結構\[/caption\] 
    4.  Cipher 內部位元推算：
    
    \[caption id="attachment_2467" align="alignnone" width="682"\]![位元推算](http://iga.candy-sky.net/wp-content/uploads/2016/04/2016-04-28_202142.jpg) 位元推算\[/caption\]
4.  產生 Nonce 與其答案：
    1.  產生 Nonce：
        
        1.  由一個 16 bit 的 LFSR 產生，稱為 PRNG（偽亂數產生器）。
        2.  產生最右邊的新 Bit：L（X0X1 . . . X15）:= X0 ⊕ X2 ⊕ X3 ⊕ X5。
        
        1.  Suc（X0X1 . . . X31） := X1X2 . . . X31L16（X16X17 . . . X31）。 \[caption id="attachment_2454" align="alignnone" width="728"\]![](http://iga.candy-sky.net/wp-content/uploads/2016/04/2016-04-28_195359.jpg) 16bits LFSR 的 PRNG\[/caption\] 
    2.  加密： \[caption id="attachment_2469" align="alignnone" width="775"\]![讀卡機 Nonce 與 答案加密](http://iga.candy-sky.net/wp-content/uploads/2016/04/2016-04-28_202400.jpg) 讀卡機 Nonce 與 答案加密\[/caption\] 

二、基本攻擊手法與名詞：

1.  The Nested Authentication（巢狀授權）：
    1.  讀取 Default Sector，其 Key 應為預設。
    2.  取得授權後，再次要求存取目標 Sector，此時卡片產生的 Nonce 將是加密過的。
2.  Parity Bit（同位元）：
    1.  在 Nonce 中，每一個 Byte 會搭配一個 Parity Bit，以方便驗證 Key 正確性。
    2.  驗證時，必先驗證 Parity Bit，若正確，才會繼續進行後續的驗證。 \[caption id="attachment_2472" align="alignnone" width="604"\]![2016-04-28_214903](http://iga.candy-sky.net/wp-content/uploads/2016/04/2016-04-28_214903.jpg) Parity Bit 產生與加密\[/caption\]

三、攻擊二代卡：

1.  前提：先使用 Nested Authentication 方法，使得卡片 Nonce 為已加密的狀態。
2.  離線暴力破解：
    1.  Key 的樣本空間：248。
    2.  取得 { Nonce } 某一 Byte 以及其 { Parity Bit }。
    3.  挑選一候選 Key，分別對 { Nonce } 以及 { Parity Bit } 進行解密。
    4.  用解密後的 Nonce 計算其 Parity Bit。
    5.  與先前解密後的 Parity Bit 進行比較：
        1.  若此候選 Key 是正確的 Key：兩者相同。
        2.  若此候選 Key 是錯誤的 Key：兩者有 50% 機率相同。
    6.  平均需 48 組 Parity Bit （12 組 Nonces）比對正確才能確認此 Key 是正確的。
3.  減低空間複雜度的方法：
    1.  計算 Sum Property：
        1.  一個 Byte 有 28 = 256 種組合。
        2.  我們蒐集獲取 256 組 Nonce，一個 Nonce 為 32bits，即 4Bytes。我們取每一組 Nonce 的第一組 Byte。
        3.  將其 8bits 做 XOR，再與其 Parity Bit 做 XOR。 \[caption id="attachment_2476" align="alignnone" width="432"\]![](http://iga.candy-sky.net/wp-content/uploads/2016/04/2016-04-29_132553.jpg) Sum Property\[/caption\]
        4.  最後的結果可能為 0 或 1，我們重複第 3 步驟，直到所有 Nonce 的第一組 Byte 都做完。
        5.  全部加總，結果可能為 0 ~ 256。 \[caption id="attachment_2478" align="alignnone" width="739"\]![Sum Property 機率分布](http://iga.candy-sky.net/wp-content/uploads/2016/04/2016-04-29_133107.jpg) Sum Property 機率分布（樣本空間為 8192 組 Nonces）\[/caption\]
    2.  分離 Sum Property：
        1.  分成 Odd 跟 Even 的 Sum Property，兩者主要可由下表公式產出。兩個 Sum Property 範圍皆為 0 ~ 16。 \[caption id="attachment_2480" align="alignnone" width="654"\]![2016-04-29_134426](http://iga.candy-sky.net/wp-content/uploads/2016/04/2016-04-29_134426.jpg) Odd 與 Even Sum Property 公式\[/caption\] 
        2.  我們定義兩個 Table 為所有 Odd Sum Property 以及 Even Sum Property 的樣本空間。 \[caption id="attachment_2481" align="alignnone" width="641"\]![Table](http://iga.candy-sky.net/wp-content/uploads/2016/04/2016-04-29_134651.jpg) Table\[/caption\]
    3.  建表對照
        1.  現在我們可以透過公式： S = p(16 - q) + (16 - p)q ，利用 Odd Sum Property 以及 Even Sum Property 計算出 Sum Property。並透過上面的表，我們可以得知在 Cipher 中，所有可能的排列方式。
        2.  我們建一個 Table，有 Sum Property、對應的 Odd Sum Property、Even Sum Property 以及 Cipher 內部狀態。接著，我們將第一步所計算出來的 Sum Property 進行對照（即 256 組 Nonces 所計算出來的 Sum Property）。假設 Sum Property 為 128，我們便刪去 Table 上除 128 以外的其他可能。剩下的即能透過組合 Odd 以及 Even ，產出一 39bits 的序列（即 Cipher 內部狀態）。
            *   由於產生 Key Stream 時，會對 Filter Function 造成影響的僅有第 9 個以後的 Bit，於是我們便只生產出 39 bit。
        3.  空間複雜度由 239，透過此建表刪去的方式，降為 236.72。
4.   進一步減低空間複雜度：待補
    1.  第八時間的 Sum Property
5.  差分分析：待補
6.  Filter Flip Property：待補