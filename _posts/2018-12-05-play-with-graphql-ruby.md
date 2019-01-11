---
layout: post
title:  "試玩 graphql-ruby 套件"
tags: [Ruby, GraphQL, 技術札記]
---

[上一篇](https://erictyl.github.io/2018/11/11/GraphlQL_is_not_super.html)談 GraphQL 的文章僅止於大量閱讀後的推測，既自詡成為一個優秀的工程師，還是該親自體驗一下這個工具，看看它能否帶來意外的驚喜。不然現在 GraphQL 在個人心目中實屬雞肋，像是個轉移痛點而非根治問題的偏方。

GraphQL 目前對多數語言的支持程度並不高，臉書只定義規格而已，之前也僅實作 Nodejs 版本[^2]，其他語言與框架都是靠社群自行按規格實作，沒有什麼官方支持。

> In order to be broadly adopted, GraphQL will have to target a wide variety of backends, frameworks, and languages, which will necessitate a collaborative effort across projects and organizations. This specification serves as a point of coordination for this effort.
> 
> --from [facebook/graphql](https://github.com/facebook/graphql#overview)

Ruby 社群使用的是 [rmosolgo / graphql-ruby](https://github.com/rmosolgo/graphql-ruby)，文檔 Goals 裡清楚地寫著[支援和發展方向](https://github.com/rmosolgo/graphql-ruby#goals)，要在 Rails 以外的地方使用會比較困難。以下紀錄在 Rails 上嵌入該套件的使用心得、建議。

**注意**：本篇截稿時間為 2018 年底，graphql-ruby 套件的 Getting Start 文檔、錯誤回報程式目前狀況還很多，時有誤導的情形發生，且正逢改版適應期[^4]，網路充斥著新舊版本範例，混用會大幅增加除錯複雜度。 
{: .notice--warning}

# 概覽 graphql-ruby Guide 再試玩
筆者習慣先跑官方範例來學習，摸個大概再去讀文件，沒想到這次卻踩到了前後文不對、少了部分基本步驟的雷。另外事後反省覺得應該先讀[文件](http://graphql-ruby.org/guides)才不至於往沒意義的方向除錯。當然文件量不少，這裡幫忙挑出最少的啟動資訊：

### Class-Base API 語法
強烈建議使用 1.8 版本後的 Class-Base API 語法，新舊語法不完全相容，統一語法能更快排除錯誤，助己助人。 [Doc](http://graphql-ruby.org/schema/class_based_api.html)

### Query & Mutation
`project_name_schema.rb` 裡可以看到 Query / Mutation 兩種型態，把它們分別視為讀、寫資料即可。另外這兩者可說是 GraphQL 的根，自定義的 API 都應該由它們起頭。**只開 Query API 有可能報錯說問題在 Mutataion API，請相信自己往 Query API 方向除錯。** [Doc](http://graphql-ruby.org/schema/root_types.html)

### Types
`project_root/app/graphql/types/` 底下放所有資料型態的定義，包含 GraphQL 原始規格和自定義的部分。其實這邊就是開 API 的地方，前述提到所有 API 的起頭`Query` / `Mutation`也在這。 [Doc](http://graphql-ruby.org/guides#type-definitions-guides)

### Fields
如果比擬 Types 為資料表（Table）的話，那麼 Fields 就是欄位（column）。資料庫撈出來的資料欄位會和 Types 定義的同名 Field 拼在一起。另外 Field 也包含虛擬欄位的概念，把虛擬欄位想成是寫 controller actions 即可，畢竟是定義 GraphQL API，不是定義 model 資料。 [Doc](http://graphql-ruby.org/fields/introduction.html)

### Arguments
Fields 底下有 Arguments，當成是 controller actions 的參數們就好，但有嚴格的語法定義，詳細可參考文件範例。 [Doc](http://graphql-ruby.org/fields/arguments.html)

### graphiql 查詢介面
套件安裝流程跑完會自動加入 graphiql，瀏覽器輸入`localhost:3000/graphiql`以開啟互動式查詢介面，它能自動產生 API 文件。 [Doc](https://github.com/graphql/graphiql)

# graphql-ruby 官方入門範例修正
Getting Started 存在的意義就是讓人無痛生出一個範例試玩，很明顯 graphql-ruby 表現不及格。主要原因如下：

* 前後文不一致。
* 少了定義資料庫步驟。

以下附上前後文一致且語法統一的 Getting Started，文章架構比照官方：

### Getting Started
跑第一個指令就好，第二個指令請不要執行，它是前後文不對的濫觴。

```
# EXECUTE THIS COMMAND
rails g graphql:install
# DO NOT EXECUTE THIS COMMAND
rails g graphql:object Post title:String rating:Int comments:[Comment]
```

### Generate models
官方完全少了這步驟，`rails g graphql:object ... `並不會建立資料庫，只開 GraphQL API 而已。想當然沒有這步驟 API 怎麼打錯，請按順序補完下列動作。

```shell
# 建立 model：
rails g model Post title truncated_preview
rails g model Comment content post:references
```

```ruby
# 補齊關聯：
# app/model/post.rb
class Post < ApplicationRecord
  has_many :comments
end

# app/model/comment.rb
class Comment < ApplicationRecord
  belongs_to :post
end
```

```shell
# 完成資料庫定義：
rails db:migrate
```

```ruby
# 寫測試資料：
# db/seeds.rb
5.times do |i|
  Post.create title: "Post_#{i + 1}", truncated_preview: "No.#{i}"
  2.times do |ii|
    c = "content_#{i + 1}_#{ii + 1}"
    Comment.create content: c, post_id: i + 1
  end
end

```

```shell
# 餵入測試資料
rails db:seed
```

### Declare types
```ruby
# app/graphql/types/post_type.rb
module Types
  class PostType < Types::BaseObject
    field :id, ID, null: false
    field :title, String, null: true
    field :truncated_preview, String, null: false
    field :comments, [Types::CommentType], null: true
  end
end

# app/graphql/types/comment_type.rb
module Types
  class CommentType < Types::BaseObject
    field :id, ID, null: false
    field :content, String, null: true
  end
end
```

### Build a Schema
```ruby
# app/graphql/types/query_type.rb
module Types
  class QueryType < Types::BaseObject
  
    # First describe the field signature:
    field :post, PostType, null: true do
      description "Find a post by ID"
      argument :id, ID, required: true
    end

    # Then provide an implementation:
    def post(id:)
      Post.find(id)
    end
  end
end
```

### Execute queries
官方這段和之後的內容可直接忽略，伺服器跑起來再用瀏覽器開 graphiql 查詢介面來玩即可。本地端 graphiql 介面網址為：[localhost:3000/graphiql](http://localhost:3000/graphiql)。

```shell
# 跑伺服器
rails s
```

```javascript
// 查詢指令範例
{
  post(id: 1) {
    title
    id
    truncatedPreview
    comments {
      id
      content
    }
  }
}
```

# 結論
截至 2018 底，graphql-ruby 套件還是不太穩定，產品若要使用這項技術，工程團隊的底子要非常高，可預期許多問題得自行解決[^7]。它有提供非常好用的查詢介面(graphiql)，而且做到了程式碼即文檔，可以自動產生 API 文件。這兩點雖值得肯定，但在 Ruby 生態圈並沒有比成熟且泛用的 RESTful 好上十倍[^8]，所以 GraphQL 定位不是眾望所歸的取代者，比較像可能替代品而已。讓我們繼續觀望。


[^2]: 參考[這邊](https://github.com/graphql/graphql-js/graphs/contributors)，主要貢獻者身份是前臉書工程師。現在好像也是社群維護，臉書不主導了。
[^4]: 同樣功能的 API 新舊版[寫法並不同](https://github.com/rmosolgo/graphql-ruby/wiki/How-To:-Use-prepare-to-modify-or-validate-arguments-or-inputs)。附上[版本發行資訊](https://rubygems.org/gems/graphql/versions)，2018 年變動著實不小。
[^7]: 反過來說，很有機會成為著名套件的貢獻者，大幅增加開發者和公司的知名度 : )
[^8]: 更何況 RESTful 也早有好用的查詢介面，且能做到自動產生文檔。看看行之有年的 Swagger UI / Grape API framework。