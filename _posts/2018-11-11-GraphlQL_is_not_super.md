---
layout: post
title:  "試論 GraphQL 所承諾的好處"
tags: [GraphQL, 技術札記]
---

焦慮與恐懼促成了這篇文章。QraphQL 主打的賣點有三，首先是前端只要面對單一介面，大幅降低開發成本；其次是前端調用資料的彈性大幅增加，可以當作整個服務被抽象成一個資料庫，而前端能直接對它下各種查詢指令；再者，若原來有多個請求（requests）才足以取得渲染前端的資料，那麼可透過 GraphlQL 包裝成單一請求，降低前端到伺服器間的總請求數。

乍看上述幾點都極具吸引力，但只怕是把難題轉嫁給後端，沒有根本解決問題。此即後端工程師的焦慮與恐懼所在[^1]。

# 簡單分析
大概可分成兩個方向討論：

* 原有多個請求，但最終向同一個資料庫要資料，那麼不應貿然導入 GraphQL，而是優化資料庫的查詢指令（不管是 SQL / NoSQL 指令，讓查詢從多個變一個，如善用 JOIN），再由後端整理成一個 API 返回結果。和選用 GraphQL / RESTful[^2] 不必然相關了，對吧？
* 原有多個請求，到伺服器端之後向不同資料來源（各個 API 伺服器、資料庫叢集）要資料，那麼將面對整理各方資料的困難。GraphQL 無法解決這個問題，只是把它從前端處理，轉移到後端處理罷了。

GraphQL 就是把盤根錯節的服務們抽象化，讓前端能單純地將它們視為一個資料庫。但請小心，如此的抽象化，對整個系統來說也許不過是挖東牆補西牆而已。

又不免聯想到約爾趣談軟體有篇文章叫做[別讓架構太空人嚇到你](https://www.joelonsoftware.com/2001/04/21/dont-let-architecture-astronauts-scare-you/)。且讓我節錄幾段深刻的文字，原文廢話就不完全引述，簡單用括弧內容代替。

> These things might be good architectures, they will certainly benefit the developers that use them, but they are not (...SUPER！)

> (...New Techs may be cool), but it doesn’t really let you do anything you couldn’t do before using other technologies — if you had a reason to.



# 小結
在還未親自嘗試 GraphQL 的前提下，目前認為它所承諾的好處都幻滅了：

* 面對單一介面是假的，GraphQL / RESTful 都可以做。
* 增加前端調用資料彈性是假的，要享有這種彈性，得先在後端拼湊所有服務。背後資料來源若很繁雜，合併各方資料本身就是難題，GraphQL 並沒有繞過它，GraphQL 端到各個資料來源之間還是要面對。
* 多個請求包成單一請求以降低連線負擔也是假的，GraphQL / RESTful 都可以做。

倒不是說不能用 GraphQL，如果後端語言、框架對 GraphQL 有良好支援，且前端人員也願意一起面對合併各方資料的難題，那不妨在新專案使用看看。私下也很想知道，使用 GraphQL 後會不會讓人對它完全改觀。

[^1]: 參考[對岸某阿里工程師說明](https://www.zhihu.com/question/38596306/answer/345281442)、[對岸某前端工程師說明](https://www.zhihu.com/question/38596306/answer/79714979)
[^2]: RESTful 是相較於 GraphQL 而言，更為普遍且成熟的 API 規格。
