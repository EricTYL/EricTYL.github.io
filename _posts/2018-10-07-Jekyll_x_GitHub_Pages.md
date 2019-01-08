---
layout: post
title:  "部落格問世：Jekyll x GitHub Pages"
tags: [Ruby, Jekyll, blog, Markdown, 技術札記]
---

陸陸續續使用 Markdown 寫了不少筆記，不時想找個支援此格式的平台，來呈現平常都用時間換了些什麼。於是嘗試用 [HackMD](https://hackmd.io/) / [logdown](http://logdown.com/) 寫點文章，也觀望了 [Medium](https://medium.com/) 一陣子，但都不太令人滿意。前些日子才發現 Ruby 生態圈早有這麼一個開源的部落格套件，稍微研究後即發現它滿足了所有個人需求，真可謂相見恨晚。

來說說 Jekyll 滿足了我什麼：

* 支援 Markdown - 個人所有筆記都是 Markdown 文章，若要把筆記轉成其他格式或重新排版實在太痛苦。於我而言這可是 Medium 唯一的敗筆，卻也是最大的敗筆。
* 介面簡明優雅 - 所謂 Less Is More 並不容易實現。
* 上手容易 - 由於 [Jekyll 文件](https://jekyllrb.com/docs/)寫相當地好，且本身已經習慣使用 Ruby 套件與 git + GitHub 版控流程，架起整個部落格到測試文章不用一個小時。

接著，來聊聊 Jekyll 以及 GitHub Pages 分別為何物，把自認過去的我會想先瞭解的資訊整理一下。

# Jekyll 簡介

> Transform your plain text into static websites and blogs.
> 
> -- Jekyll official site

誠如[官網](https://jekyllrb.com/)所言，用 Ruby 寫成的 Jekyll，主要功能就是將使用者的文章轉換成 HTML 靜態頁面。同時，GitHub 也提供方便的 host 服務，把部落格的 git repository 放到 GitHub 上即可完成架站，非常方便。 

前面提過 Jekyll 吸引我的地方，這裡更進一步列舉它的優點：

* 不需要資料庫 - 文章保存在 git repo，直接享有版本控制的好處。
* 頁面快速讀取 - 因為所有頁面（文章、首頁等）全是已轉換後的 HTML 靜態頁面。
* 輕巧卻不失擴充空間 - Jekyll 本身限縮功能在寫文章與存取服務上，進階功能如 tag、留言、廣告等都不提供，但 Jekyll 開源生態裡這些都有了，可用現成套件或自幹功能解決。
* 架站方便 - GitHub 提供免費且極易操作的 Jekyll 整合服務，要求只有 repo 名稱要符合格式。
* 高客製化彈性 - Jekyll 完全開源，畫面可以自訂或使用各種開源主題。
* 文件完善 - 這也間接成就了架站方便和高客製化彈性。
* 廣大社群支持 - 不僅是許多個人在用，非常多專業組織也使用 Jekyll[^3]。

# GitHub Pages 簡介

> Websites for you and your projects.  Hosted directly from your GitHub repository. Just edit, push, and your changes are live.
> 
> -- GitHub Pages official site

GitHub Pages 是每個 GitHub 用戶都有的免費靜態頁面空間[^5]，或稱使用者頁面。要注意的是一個帳號只會有一個使用者頁面`username.github.io/`，但可以有許多專案頁面`username.github.io/project_name`。本文和官方教學都是把部落格架在使用者頁面，而非專案頁面，要改架在專案頁面還請自行研究。

# 架站流程

這裏盡可能詳盡地提供所有步驟，包含一個 Rails 工程師直接跳過的部分。

刪除線為個人跳過的事項；`$ command xxx`錢號代表終端機，不是指令的一部分；而前綴 optional 的事項則不一定要進行。

* ~~安裝最新穩定版的 Ruby~~
* 安裝 Jekyll 套件`$ gem install bundler jekyll`
* 使用`$ jekyll new your_blog`指令產出部落格專案資料夾
* (optional) 進入`your_blog`資料夾後，使用`$ jekyll s`在本地端架站，並用瀏覽器訪問`localhost:4000`部落格預設網址查看效果。
* 在 `your_blog` 資料夾初始化程式碼倉庫，步驟為：`$ git init` -> `$ git add .` -> `$ git commit -m 'init repo'`
* ~~開通 GitHub 帳號~~
* 新增 GitHub Pages repo，注意專案名稱須為`username.github.io`
* 把本地`your_blog`專案和`username.github.io`專案同步，建議參考上一步建完專案後 GitHub 提供的同步指令範例。
* 用瀏覽器訪問`username.github.io/`查看是否有 Jekyll 預設畫面

剩下的，就只要在本地`.../your_blog/_posts/`按規格[^7]寫 Markdown 文章，上傳步驟走一般 GitHub 版控流程即可。

另外附上兩者的官方教學：[Jekyll 安裝步驟](https://jekyllrb.com/)、[GitHub Pages 設定步驟](https://pages.github.com/)。

[^3]: 有誰在用 Jekyll？[Spotify / Twitch / Netflix / 白宮 SBST / Rails / ...](https://jekyllrb.com/showcase/)。
[^5]: git / GitHub / GitHub Pages 的關係：git 是版本控制軟體，而 GitHub 為使用 git 並提供程式碼代管服務的供應商。又 GitHub Pages 是 GitHub 為每個用戶提供的靜態網站空間，常用來放部落格或專案品牌頁面。
[^7]: 比如檔名須為 yyyy-mm-dd-title.md 等等，Jekyll 規範請參訪[官方文件](https://jekyllrb.com/docs/posts/)。

# 部落格開通後，是全新挑戰的開始

持續寫作很辛苦。

會讀到這裡的朋友，相信也是有心經營部落格的同好吧，別忘了寫作的初衷，我們共勉之。

2018 程式寫作之路，啟航。
