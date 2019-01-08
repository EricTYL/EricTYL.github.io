---
layout: post
title: "Selenium x Ruby 系列起手式"
tags: [Ruby, Selenium, 爬蟲, 技術札記]
---

除了網站開發與維護，撰寫爬蟲也是不少後端工程師的工作項目，而眾多爬蟲開發工具裡的 [Selenium](https://github.com/SeleniumHQ/selenium)，因其操作流程更貼近一般使用者，成為了爬蟲、網頁前端測試的一時之選。也由於 Selenium 支援眾多程式語言，包含筆者愛用的 Ruby，所以被納入了武器庫中，此系列文由此而生。

本文會先比較 Selenium 與 HTTP 連線工具，並交代前置作業與結論，下一篇才進入實作。

# Selenium vs. HTTP 連線工具們

為何說 Selenium 更貼近一般使用者，就在於它直接操作瀏覽器，因此瀏覽器私下幫我們做的事就不用額外寫了。列舉如下：

* 塞 headers 讓網站判斷人機操作（user or bot）
* 塞 cookies 讓網站確認使用者狀態（user status and client data）
* 渲染前端頁面（超級有感）

而 Unix-like 底下的 [curl](https://github.com/curl/curl) [^1]、Ruby 家的 HTTP clients（[faraday](https://github.com/lostisland/faraday)、[httprb/http](https://github.com/httprb/http)、[...](https://www.ruby-toolbox.com/categories/http_clients)）都要額外處理上述事項。

***Selenium 帶給筆者的另一震撼，是幾乎不用先備 HTTP protocol 相關知識。***

一般人上網並不須要懂 GET / POST 等為何物，只管點滑鼠就好，使用 Selenium 套件可充分發揮這個優點，因此對新手來說 Selenium 比各家 HTTP clients 函式庫更易上手（何況還要另外學相應的 HTML parser）。身為 Web 工程師不可不知 HTTP protocol，但此系列文確實不需要相關知識。

當然它也有其不足處，以下列舉幾個其他工具的優勢：curl 可以打許多 HTTP 以外的連線協定；串接 API 還是 HTTP clients 更快更方便，因為不用解析 HTML。

了解各方優缺，才知道什麼情境適合什麼工具。

整個系列文將以 [Michael Chen 部落格 Selenium 網路爬蟲](https://cwchen.tw/selenium/) 的前幾篇作為題目，且換用 Ruby 而非 Python 來實作，之後再自訂一些案例來驗證是否已能掌握 Selenium。



# 前置作業
確認有可用的 Ruby 版本：

```console
foo@bar:~$ ruby -v   # 取得當前 Ruby 版本，筆者使用 ruby 2.5
```

安裝 Selenium 的 Ruby 套件 selenium-webdriver：

```console
foo@bar:~$ gem install selenium-webdriver 
```

安裝個人偏好的瀏覽器 webdriver [^2]，如平常用 Chrome 就對應到 chromedriver，筆者使用蘋果電腦所以抓 mac 專用的版本。把解壓縮後的檔案放到`\usr\local\bin`即可。蘋果用戶也可以透過 Homebrew 安裝。

chromedriver 載點： [http://chromedriver.chromium.org/downloads](http://chromedriver.chromium.org/downloads)

# 結論：為何學習 Selenium？

* 更貼近一般上網行為
* 方便擷取前端渲染的資訊[^7]
* [文件](https://seleniumhq.github.io/selenium/docs/api/rb/)、線上資源豐富
* 支援多數主流程式語言
* 幾乎不用擁有 HTTP protocol 知識
* 部分情境下學一套抵兩套 [^8]
* 專案仍然活躍，可預期會持續維護[^9]
* 未來前端測試可用[^10]

[^1]: 如果只打 HTTP 協定，推薦 Mac 用戶使用 [HTTPie](https://httpie.org/)，它的介面很漂亮。
[^2]: 注意不是抓最新的 webdriver 就可以，而是要挑與目前瀏覽器匹配的 webdriver。
[^7]: 前端渲染指的是伺服器傳送包含 javascript 程式的 html 頁面給瀏覽器，瀏覽器再根據用戶行為動態完善相應資訊。相反的，後端渲染則為伺服器已把動態資訊跑完，產出純 html 頁面給瀏覽器，比如 Rails 預設使用的 Embedded Ruby （[erb 範例](https://puppet.com/docs/puppet/5.3/lang_template_erb.html)）。私以為前端渲染、後端渲染改稱瀏覽器端填空、伺服器端填空更直白。
[^8]: 兩套指的是 HTTP 連線工具 + HTML 解析器，像是 Ruby 家的 faraday + Nokogiri、Python 家的 requests + BeautifulSoup。
[^9]: 文章撰寫於 2018 年底，可參考 Selenium [過往增修狀況](https://github.com/SeleniumHQ/selenium/graphs/contributors?from=2004-10-31&to=2018-11-08&type=c)。
[^10]: Selenium IDE 測試版[支援谷歌和火狐](https://seleniumhq.wordpress.com/2018/08/06/selenium-ide-tng/?utm_source=feedburner&utm_medium=feed&utm_campaign=Feed%3A+Selenium+%28The+Official+Selenium+Blog%29)瀏覽器。
