---
title: Wordpress 被植入 SEO Spam 的清除事件簿
categories:
  - 軟體研討
date: 2016-12-07 10:55:19
tags:
---

昨天晚上，正當我在實驗室埋首於樂天購物網站的五折搶購時，老闆發訊息來了：「幫忙看一下，抓出來，打手槍打到死。」

![](https://imgur.com/6Uh2Mci.jpg)

原來是 Google 在老闆的網站上掛了一個「這個網站可能已遭入侵。」，不過老實說，我是第一次看到這個，身為一個二十年西瓜，不知道怎麼做，先點進去看看好了。

![](https://imgur.com/xB6mA7S.jpg)

Google 直接告訴我們有哪些頁面被插入可疑的程式碼片段，看來是廣告垃圾，SEO Spam，透過我們的網站對別的網站做 SEO。

![](https://imgur.com/cbCp8Vv.jpg)

直接點開網頁原始碼，被插入 8 行左右，class都是qpr，直接塞在body第一行，而且插入了一段css把廣告連結塞到螢幕顯示範圍外。由於在原始碼顯示，所以應該可以排除從js塞進來的。

Jyny 建議先進到 wordpress 根目錄下 grep 尋找有沒有檔案被插入關鍵domain字串。

```Console=
grep -rnw ./ -e "valiumprice.com"
```

只有找到快取有這串，於是先把快取清掉，之後就找不到了，看來不是直接藏在檔案內。
打開SQL，直接尋找wp_posts的content，也是沒有找到。

這時發生一件事情，我在插件中尋找線索的時候，網站突然 HTTP 500 了，我束手無策。讓 Tamama 和 Jyny 來救援，先找 nginx log 來看看，過程中也發現 server 中留有前一個網管的另一個網站，看來的確是有用這台主機跑其他服務XD

這時火文狼和 Danny 大大回來了，於是一群人在電腦前大顯身手，重設定 nginx 跟喝水一樣，嚇到我要哭出來了。重設之後找到拋出例外而使內部錯誤的插件，刪除後成功進入網站。並花了一點時間重整了網站版面。

半夜，經火文狼提點，在wordpress的header.php當中第16行被插入一段 php code，運行wordpress的function「get_option」
從資料庫抓資料來直接運行。這串「\x72\x65\x6e\x64\x65\x72」解碼後是「render」。
而這段code恰好插在body_class()被呼叫之後，看來應該就是了。

![](https://imgur.com/8qkEGnA.jpg)

於是從資料庫尋找 option_name 為「render」的 option，的確有找到一個，而且內容是一串很長的base64。
Delete之後把被插入的php code拿掉，回到網頁就正常了。

網站原始碼的 <body> 底下沒有看到被插入的連結。

![](https://imgur.com/0c8hWi0.jpg)

感謝好友火文狼、Jyny、Tamma、Danny、Kid 們幫助我解決 500 問題，重新設定 nginx 並找到問題插件。還幫忙尋找此次的 SEO Spam 和其他問題。身為一個西瓜感激不盡。