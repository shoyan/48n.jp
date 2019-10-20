---
layout: post
title: "Rails5からbelongs_toアソシエーションの挙動が変わった"
date: 2017-01-13 15:44:48 +0900
comments: true
categories: rails
description: "Rails5からbelongs_toアソシエーションの挙動が変わった。親モデルに属している子モデルは親モデルの外部キーが存在しないとバリデーションエラーになるのがデフォルトの挙動となっている。"
---

Rails5から `belogs_to` アソシエーションの挙動が変わった。

親モデルに属している子モデルは親モデルの外部キーが存在しないとバリデーションエラーになるのがデフォルトの挙動となっている。
参照先レコードのidをnullで登録しようとした場合は以下のようなバリデーションエラーが出る。

```
ActiveRecord::RecordInvalid: Validation failed: Post  must exist
```

DHHの一声で `belogs_to` の挙動が変わる様が垣間見れるイシュー。

* https://github.com/rails/rails/issues/18233

DHHの言い分としては「普通 belongs_to って、参照先テーブルのIDは必ずもってるだろ。ならデフォルト required: true でよくね？」という話。

この挙動をRails4と同じにしたい場合は `optional: true` を使う(`required: false` は廃止予定なので使わないほうがいい)。


```ruby

class Post < ApplicationRecord
  belongs_to :article , optional: true
end

```

`belongs_to` のオプションの詳細については以下のドキュメントで参照できる。

* http://api.rubyonrails.org/classes/ActiveRecord/Associations/ClassMethods.html#method-i-belongs_to
