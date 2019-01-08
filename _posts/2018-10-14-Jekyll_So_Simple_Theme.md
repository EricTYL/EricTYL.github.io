---
layout: post
title:  "Jekyll 風格主題之選擇"
tags: [Ruby, Jekyll, blog, Markdown, 技術札記]
---

Jekyll 的預設主題其實有點簡陋，許多重點功能沒有提供，而且不太有設計感。還好這些我們可藉由第三方彌補，只要釐清自己的需求，並不難找到合適的第三方主題來用。個人理想主題裡需要涵蓋下列項目：

* 分類功能
* 標籤功能
* 不花俏的設計
* 文章列表分頁功能
* Markdown 的連結、高亮、程式碼區塊等特殊表現夠明顯
* 支援留言功能

經過一兩個小時的瀏覽後，鎖定以下三個候選主題：

* [so-simple-theme](https://github.com/mmistakes/so-simple-theme) 及其[示範頁面](https://mmistakes.github.io/so-simple-theme/)
* [Huxpro](https://github.com/Huxpro/huxpro.github.io) 及其[示範頁面](http://huangxuan.me/)
* [lanyon](https://github.com/poole/lanyon#themes) 及其[示範頁面](http://lanyon.getpoole.com/)

# 就是要每件事情都 So Simple

三者都足夠好看，但 lanyon 示範頁沒有分類項，且文件概覽後不若其他兩者清楚，猜測套用會比較麻煩，於是首先放棄。Huxpro 和 so-simple-theme 都很好，但因為 so-simple-theme 的示範頁能清楚呈現各個功能及對應的具體效果，而 Huxpro 示範頁就是作者的個人部落格，不若 so-simple-theme 那樣有系統的 demo 特定功能，因此最後選定 so-simple-theme。

實際爬文件和試用後，so-simple-theme 並沒讓人失望，推薦有興趣的同好使用，有能力也別吝於給原作者贊助回饋，主題的 github 頁面有 paypal 按鈕 :)

# 後記

值得一提的是，該主題的分類功能有按年份日期分類（posts）、自訂分類（categories）、標籤分類（tags）三種，從實作和使用來看，自訂分類（categories）和標籤分類（tags）根本一模一樣，幾經思量後決定捨棄自訂分類，保留按年份跟標籤分類的功能即可。

測試的過程中還踩了一個雷，如果文章紀錄時間超過現實時間，是不會出現的。測試時隨手打了篇 2020 的文章，一直沒有出現，還以為是 bug 呢，原來是隱藏偷跑文章的功能啊。so-simple-theme 其實支援[顯示未來日期的文章](https://mmistakes.github.io/so-simple-theme/post/post-future-date/)，有這種需求設定一下就好。
