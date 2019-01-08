---
layout: post
title:  "Selenium x Ruby 爬取 OFX 外匯保證金交易資料"
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

1. 前往 [OFX 的歷史匯率頁面](https://www.ofx.com/en-au/forex-news/historical-exchange-rates/)
2. 點選基礎匯率 (base currency) 的箭頭
3. 選擇我們想要的基礎匯率，比如歐元 EUR
4. ~~再點選基礎匯率的箭頭以關閉該選項框~~
5. 點選目標匯率 (target currency) 的箭頭
6. 選擇我們想要的目標匯率，比如美金 USD
7. ~~再點選目標匯率的箭頭以關閉該選項框~~
8. 單選取樣頻率
9. 選擇時距 (durtion) 的箭頭
10. 選擇我們想要的時距，像是 5 年
11. ~~再選擇時距的箭頭以關閉該選項框~~
12. 按下送出 (submit) 按鈕
13. (需使用爬蟲) 抓取歷史匯率資料並寫入 CSV 檔案中

# 完整程式

```ruby
# 引用套件和 std lib 的 csv 模組
require 'selenium-webdriver'
require 'csv'

# 各項資訊參數化
url = 'https://www.ofx.com/en-au/forex-news/historical-exchange-rates/'
base_currency = 'EUR Euro'
target_currency = 'USD US Dollar'
frequency = 'yearly'
reporting_period = 'Last 5 years'

# 指定爬蟲使用 chrome webdriver
driver = Selenium::WebDriver.for :chrome

# 1. 前往 [OFX 的歷史匯率頁面
driver.get url

sleep rand(3..5)


# 2. 點選基礎匯率 (base currency) 的箭頭
base_currency_element = driver.find_element(:css, "div.historical-rates--camparison--base .select2 .selection span .select2-selection__arrow")
base_currency_element.click

# 3. 選擇我們想要的基礎匯率，比如歐元 EUR
base_options = driver.find_elements(:css, ".historical-rates--camparison--base select optgroup[label=\"All Currencies\"] option")
base_options.each do |option|
  if option.text == base_currency
    option.click
    break
  end
end

sleep rand(3..5)

# 4. 再點選基礎匯率的箭頭以關閉該選項框
base_currency_element.click

sleep rand(3..5)

# 5. 點選目標匯率 (target currency) 的箭頭
target_currency_element = driver.find_element(:css, "div.historical-rates--camparison--target .select2 .selection span .select2-selection__arrow")
target_currency_element.click

# 6. 選擇我們想要的目標匯率，比如美金 USD
target_options = driver.find_elements(:css, ".historical-rates--camparison--target select optgroup[label=\"All Currencies\"] option")
target_options.each do |option|
  if option.text == target_currency
    option.click
    break
  end
end

sleep rand(3..5)

# 7. 再點選目標匯率的箭頭以關閉該選項框
target_currency_element.click

sleep rand(3..5)

# 8. 單選取樣頻率
# 時間單位：Daily, Monthly, Yearly
frequency_options = driver.find_elements(:css, "li.col-xs-4 div.form-control--radio input")
frequency_options.each do |radio|
  radio.click if radio.attribute('value') == frequency
end

sleep rand(3..5)

# 9. 選擇時距 (durtion) 的箭頭
# "~" 在下行 :css 路徑中代表上一層 element 的意思
frequency_element = driver.find_element(:css, ".historical-rates--period ~ .select2 .selection span .select2-selection__arrow")
frequency_element.click

# 10. 選擇我們想要的時距，像是 5 年
frequency_opts = driver.find_elements(:css, "select.historical-rates--period option")
frequency_opts.each do |option|
  if option.text == reporting_period
    option.click
    break
  end
end

sleep rand(3..5)

# 11. 再選擇時距的箭頭以關閉該選項框
frequency_element.click

sleep rand(3..5)

# 12. 按下送出 (submit) 按鈕
submit_element = driver.find_element(:css, "button.historical-rates--submit")
submit_element.click

sleep rand(3..5)

# 13. (需使用爬蟲) 抓取歷史匯率資料並寫入 CSV 檔案中
# CSV 檔案指定在爬蟲所在資料夾
current_dir = Dir.pwd

CSV.open("#{current_dir}/ofx_data.csv", "wb") do |csv|
  data = driver.find_elements(:css, ".historical-rates--results-table tr")
  data.each do |tr|
    d = tr.find_element(:css, ".historical-rates--table--date").text
    r = tr.find_element(:css, ".historical-rates--table--rate").text
    csv << [d, r]
  end
end
```

# 結語

讀者也許有注意到，手動操作的步驟 4. 7. 11. 被刪掉了，親自跑流程也沒有收闔的動作，點一下幣種、時間區間就好。然而程式沒那麼聰明，還是得補上這些步驟，免得無法正確執行。

還有使用 Selenium 操作 HTML elements 時，要避免元素關掉後還去存取，不然會噴 Selenium::WebDriver::Error::NoSuchElementError。

其實我們可以發現寫爬蟲挺瑣碎的，如果要抓的資料只用一次，根本沒有價值，人力操作反而更快。然而這類工作若要常常進行，比方說每天檢查某種資訊，那麼不妨寫支爬蟲定期代勞 : )
