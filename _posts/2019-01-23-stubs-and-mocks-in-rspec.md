---
layout: post
title:  "Stubs / Mocks in RSpec"
tags: [Ruby, Jekyll, blog, Markdown, 技術札記]
---

`stub`、`mock`這兩個概念在 RSpec 裡面，若不論最初的調用名稱，只看參數、後續的調用方法，很難摸清它們的差異。感覺都是操作目標物件（object）並呼叫它的某個方法（方法可不必先定義、實作），然後讓人自定義回傳值。參考如下：

```ruby
# Stubs
allow(obj).to receive(method).and_return(value)
allow(obj).to receive(method).with(parameter).and_return(value)

# Mocks
expect(obj).to receive(method).and_return(value)
expect(obj).to receive(method).with(parameter).and_return(value)
```

最廣為流傳的說法認為`stub`假裝物件有某個方法，若呼叫到它的話則回傳設計好的結果，不論方法是否存在、裡面的邏輯怎麼寫。**被測試的目標程式可以不觸發這個方法，開發者只在意觸發的話拿到什麼回傳值**。`mock`雖一樣假裝物件有某個方法，**但被測試的目標程式一定要觸發指定的物件方法，不管回傳值**。

其實個人對這樣的解釋不太滿意，區分時不必提到回傳值，**只要知道一個允許方法不被觸發、另一個規定方法一定要被觸發**。不論使用何者，若有需要指定回傳值，都可以透過`.and_return(value)`完成。

這是目前找到最接近個人觀點的解釋：
> Mocks are all about expectations for a method to be called, whereas stubs are just about allowing an object to respond to method call with some value.
> 
> from [Rubyblog.pro](http://rubyblog.pro/2017/10/rspec-difference-between-mocks-and-stubs)

有了上述理解，可以推論出他們個別的使用情境，參考如下。

```ruby
# 適合使用 stub 來測試的情境：方法可能不被觸發

# target.rb
if a_condition
  object.method
else
  # code without 'object.method'
end

# spec.rb
allow(object).to receive(:method).and_return(value)



# 適合使用 mock 來測試的情境：發法必被觸發

# target.rb
object.method

# spec.rb
expect(object).to receive(:method).and_return(value)
```

# 使用替身

在測試驅動開發的過程裡，通常會透過`double`產生假物件（也就是所謂的替身）來寫測試。如此，不只物件方法可以不用先定義，連物件也可以，TDD 執行起來更順了。

```ruby
# let(:car) { Car.new(owner: 'Eric', manufacturer: 'Bentley Motors Limited') }
let(:car) { double('What ever.') }

# 搭配 stub 指定回傳值是 'Go!'
allow(car).to receive(:run).and_return('Go!')

# 搭配 mock 確認 car.run 必須被呼叫，否則噴錯
expect(car).to receive(:run).and_return('Go!')
```

# 使用真身

當物件已經寫好的話，也可以直接拿來用，僅假造方法、回傳值，這種做法稱為 partial stubbing / partial mocking。

```ruby
# Partial stubbing
# 被測程式若有呼叫到 user.age，則不論 :age 是否實作，都指定回傳值為 22
user = User.new # User 類別已定義
allow(user).to receive(:age).and_return(22)

# Partial mocking
# 被測程式必須呼叫 my_pc.shut_down，且不論 :shut_down 是否實作，都指定回傳值為 true
my_pc = Computer.new # Computer 類別已定義
expect(my_pc).to receive(:shut_down).and_return(true)
```

# 結論

為求方便記憶，重點可以總結為二：

* 搭配`double`產生假物件，使用`stub`、`mock`寫測試時的彈性更大。物件方法、物件本身都不用先定義。
* `stub`允許指定方法不被呼叫、`mock`期待指定方法至少被呼叫一次。都可以指定回傳值。
