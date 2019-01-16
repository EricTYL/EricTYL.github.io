---
layout: post
title:  "使用 graphql-ruby 建立電影評論 API"
tags: [Ruby, GraphQL, 技術札記]
---
延續[初探 graphql-ruby 套件](https://erictyl.github.io/2018/12/05/play-with-graphql-ruby.html)文章，完善官方入門修訂版後，這裏再以現有知識開新 GraphQL API，好驗證知識和實作之間有沒有落差。

# 範例 API 概說
我們會建立一個電影 API 為範例，只做簡單的四件事：查詢所有電影、查詢單一電影、新增電影、新增電影評論。實際上 graphql-ruby 套件把所有 GraphQL 請求都塞到 POST 再送往 graphql_controller#execute，但這裏我們可以將四個 API 分別想像成：

* GET movie_controller#index
* GET movie_controller#show
* POST movie_controller#create
* POST review_controller#create

希望對思考暫時被 Rails / MVC / RESTful 限制住的人有幫助 : )

# 範例流程

1. 建立專案和引入套件
2. 定義資料庫和 model
3. 實作 GraphQL API

## 1. 建立專案和引入套件

這邊是套件官方前置作業，就不贅述，直接看指令、程式。

```
# 建立專案，因非產品故習慣 -T 省略測試
rails new movie_api -T

# 進入專案
cd movie_api
```

```ruby
# 在專案中引入套件

# Gemfile
gem 'graphql', '~>1.8'
gem 'faker', group: :development # OPTIONAL，但下文 db/seeds.rb 有用到
```

```
# 引入 Gemfile 中所有套件到 movie_api 專案
bundle install

# Rails 專案中建立 GraphQL API 架構，概念跟 scaffold 差不多
rails g graphql:install

# 前一指令會引入 graphiql 查詢介面套件，所以再跑一次 bundle
bundle install
```

## 2. 定義資料庫和 model

```
# 定義 Movie model
rails g model Movie title description

# 定義 Review model
rails g model Review content movie:references

# 建立資料庫和 model 定義紀錄
rails db:create; rails db:migrate
```

```ruby
# 補上 model 之間的關聯

# app/models/movie.rb
class Movie < ApplicationRecord
  has_many :reviews
end

# app/models/review.rb
class Review < ApplicationRecord
  belongs_to :movie
end

# 寫種子資料

# db/seeds.rb
movies = Faker::Movies.constants.select do |c|
  Faker::Movies.const_get(c).is_a? Class
end

5.times do |i|
  movie_id = i + 1
  Movie.create(
    id: movie_id,
    title: movies[i],
    description: Faker::Movie.quote
  )
  3.times do
    Review.create(
      movie_id: movie_id,
      content: Faker::Movies::HarryPotter.quote
    )
  end
end
```

```
# 將種子資料餵入資料庫
rails db:seed
```

## 3. 實作 GraphQL API

```ruby
# 開寫 API - QueryType / MovieType / ReviewType

# 這裏定義了查詢所有電影、查詢指定電影的 API
# app/graphql/types/query_type.rb
module Types
  class QueryType < Types::BaseObject
    graphql_name 'Query'

    field :all_movies, [Types::MovieType, null: true], null: false do
      description 'All movies'
    end

    field :movie, Types::MovieType, null: false do
      description 'A movie'
      argument :id, ID, required: true
    end

    def movie(id:)
      Movie.find(id)
    end

    def all_movies
      Movie.all
    end
  end
end

# 這裏定義一個電影在 GraphQL 中的資料長怎樣
# app/graphql/types/movie_type.rb
module Types
  class MovieType < Types::BaseObject
    graphql_name 'Movie'

    field :id, ID, null: false
    field :title, String, null: false
    field :description, String, null: false
    field :reviews, [Types::ReviewType], null: true

    def reviews
      object.reviews
    end
  end
end

# 這裏定義一個評論在 GraphQL 中的資料長怎樣
# app/graphql/types/review_type.rb
module Types
  class ReviewType < Types::BaseObject
    graphql_name 'Review'

    field :id, ID, null: false
    field :content, String, null: false
  end
end
```

```ruby
# 開寫 API - MutationType

# 這裏定義了新增電影、替指定電影新增評論的 API
# app/graphql/types/mutation_type.rb
module Types
  class MutationType < Types::BaseObject
    graphql_name 'Mutation'

    field :create_movie, Types::MovieType, null: false do
      description 'Add a movie'
      argument :title, String, required: true
      argument :description, String, required: true
    end

    field :add_a_review_for_the_movie, Types::ReviewType, null: false do
      description 'Add a review for the movie'
      argument :movie_id, ID, required: true
      argument :content, String, required: true
    end

    def create_movie(title:, description:)
      Movie.create(
        title: title,
        description: description
      )
    end

    def add_a_review_for_the_movie(movie_id:, content:)
      Review.create(
        movie_id: movie_id,
        content: content
      )
    end
  end
end
```

```
# 開啟 API 伺服器
rails s
```

瀏覽器網址輸入`localhost:4000/graphiql`開查詢介面，送出以下指令驗證各個 API

```javascript
// 查詢所有電影
query{
  allMovies{
    title
    description
  }
}

// 查詢指定電影
query{
  movie(id: 1){
    title
    reviews{
      content
    }
  }
}

// 新增電影
mutation{
  createMovie(title: "Black Mirror", description: "This sci-fi anthology series explores a twisted, high-tech near-future where humanity's greatest innovations and darkest instincts collide."){
    id
    title
    description
  }
}

// 替指定電影新增評論
mutation{
  addAReviewForTheMovie(movieId: 1, content: "AWESOME"){
    id
  }
}
```

# 結論
由上例或自行在 graphiql 查詢介面試玩時可以發現，我們開的其實不只四個 API，四個基本 API 能從所有資料欄位挑選出需要的部分，衍生出多個子 API，這是 GraphQL 寫起來最過癮的部分，也是前端熱愛 GraphQL 的原因。

在簡單架構的服務中（如本範例），我們已可體會 QraphQL 對前端多麼有善，而後端其實也不複雜，頂多要注意查詢會不會造成 N+1 問題，因此它確實有吸引人之處。但我們無法確信如下圖的架構架起來是否同樣美好，[之前的文章](https://erictyl.github.io/2018/11/11/GraphlQL_is_not_super.html)已經嘗試討論過，圖中各個服務要怎麼在 GraphQL API 端把資料自由 JOIN 將會是最大難題。

![GraphQL 終極架構圖](https://i.imgur.com/Jdvb0fV.png)
圖片來源：[Think in GraphQL 系列第一篇](https://ithelp.ithome.com.tw/articles/10200678)
