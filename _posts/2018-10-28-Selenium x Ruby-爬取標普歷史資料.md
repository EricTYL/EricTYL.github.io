---
layout: post
title:  "Selenium x Ruby 爬取 Yahoo Finance 標準普爾歷史資料"
tags: [Ruby, Selenium, 爬蟲, 技術札記]
---

為求系列文簡潔易讀，將統一每篇實作正文為手動操作步驟、完整的爬蟲程式碼、結語三大部分。同時特別聲明，此爬蟲系列文僅示範如何使用 Selenium 套件並探討其優缺，不應作為任何形式的投資輔助工具或建議，雖歡迎自由取用，但所有行為請使用者自行負責。

對於初次練習爬蟲程式的朋友，有幾件事值得提醒一下。首先，請確保不要寫出無限發請求的迴圈，或短時間大量連線的程式，以免浪費網路資源；再者，網站隨時可能改版，同支程式也許一陣子之後就要進行調整，因此工作用的爬蟲要不斷維護（藉機提醒，正文中的爬蟲不見得會跟上網站改版）。

* [Selenium x Ruby 系列起手式（x）](#)
* [爬取 Yahoo Finance 標準普爾歷史資料（x）](#)
* [爬取 OFX 外匯保證金交易資料（x）](#)
* [爬取 （x）](#)
* [爬取 （x）](#)

# 手動操作步驟

1. 前往 [Yahoo Finance](https://finance.yahoo.com/) 網頁
2. 在搜尋框輸入我們感興趣的投資標的，像是 SPY (標普指數)
3. 按下輸入鈕，刷到 SPY 所在的頁面
4. 切換到 Historical Data 所在的分頁
5. 開啟 Time Period 的設定框
6. 將時距調到 5 年 (5Y)
7. 按下 Done 按鈕關閉該設定框
8. 按下 Apply 按鈕確認該設定生效
9. 按下 Download Data 連結下載 CSV 檔案

# 完整程式

```ruby
# 引入 selenium 套件
require 'selenium-webdriver'

# 目標網頁參數化
target_asset = 'SPY'
url = 'https://finance.yahoo.com/'

# 讓 selenium 使用 chrome webdriver
driver = Selenium::WebDriver.for :chrome

# 1.前往 Yahoo Finance 網頁
driver.get url

sleep rand(2..4)

# 定錨在 input box
input_element = driver.find_element(:css, 'div#fin-srch-assist input')

# 定錨在 submit form
submit_element = driver.find_element(:css, 'div#search-buttons button')

# 連續技，包含兩個步驟：
# 2. 在搜尋框輸入我們感興趣的投資標的，像是 SPY (標普指數)
# 3. 按下輸入鈕，刷到 SPY 所在的頁面
driver.action.send_keys(input_element, target_asset)
      .click(submit_element)
      .perform

sleep rand(2..4)

# 4. 切換到 Historical Data 所在的分頁
item = driver.find_element(:link_text, 'Historical Data')
driver.action.click(item).perform

sleep rand(2..4)

# 5. 開啟 Time Period 的設定框
arrows = driver.find_elements(:css, "[data-icon=\"CoreArrowDown\"]")
arrows[1].click

sleep rand(2..4)

# 6. 將時距調到 5 年 (5Y)
durations = driver.find_elements(:css, "[data-test=\"date-picker-menu\"] div span")
durations.each do |duration|
  duration.click if duration.attribute('data-value') == '5_Y'
end

sleep rand(2..4)

# 7. 按下 Done 按鈕關閉該設定框
buttons = driver.find_elements(:css, "[data-test=\"date-picker-menu\"] div button")
buttons.each do |button|
  if button.text == 'Done'
    button.click
    break
    end
end

sleep rand(2..4)

# 8. 按下 Apply 按鈕確認該設定生效
buttons = driver.find_elements(:css, "button span")
buttons.each do |button|
  if button.text == 'Apply'
    button.click
    break
 end
end

sleep rand(2..4)

# 9. 按下 Download Data 連結下載 CSV 檔案
driver.find_element(:css, "[data-icon=\"download\"]").click

sleep rand(2..4)

# 記得要關掉瀏覽器
driver.quit
```

# 結語

在程式碼中，我們可以看到許多`sleep rand(2..4)`，其作用正如字面一樣，讓程式睡個數秒。這麼做的原因在於我們要等網頁刷完，才會有新的元素（elements）出現供接下來的程式使用，以免撲空。

上面的陳述也間接呼應了本系列首篇提過的好處：`易處理前端渲染內容`、`學一套抵兩套`。由於發請求（request）和解析回應（respose，parsing HTML）**都使用套件統一的寫法**，所以我們體會了何謂`學一套抵兩套`；且解析後也用同一套寫法處理前端渲染的內容（刷新頁面才出現的新元素），只需要指定待機時間，**不需另外寫程式觸發前端渲染**，所以我們體會了何謂`易處理前端渲染內容`。

當然，有些工作是 Selenium 力有未殆之處，還是要先釐清使用情境和每個工具的優缺點，才能在適當的時機選用適當的工具。如何在各種開發成本和目標效益之間取得平衡，可說是軟體界的中庸之道，共勉之。
