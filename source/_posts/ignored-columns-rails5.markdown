---
layout: post
title: "Rails5のignored_columnsで予約語のエラーを回避する"
date: 2017-03-06 18:28:48 +0900
comments: true
categories: rails
description: "Railsには様々な予約語がありますが、歴史の長いシステムを扱っているとDBのカラムがその予約語に該当する場合があります。rails5で追加されたignored_columnsで回避する方法を紹介します。"
---

こんにちは、SHOYANです。

Railsには様々な予約語がありますが、歴史の長いシステムを扱っているとDBのカラムがその予約語に該当する場合があります。
例えば `class`というカラム名があった場合、そのカラムの影響で以下のエラーが発生します。

```ruby
pry(main)> User.all.first
NoMethodError: undefined method `fetch_value' for nil:NilClass
```

これを回避する方法の1つがDBのカラム名の変更です。
しかし、DBのカラム名の変更は様々な影響が考えられるため簡単に変更できない場合があります。

その場合はアプリケーション側で回避します。

## Rails5のignored_columnsで予約語のエラーを回避する

Rails5で追加された [ignored_columns](http://api.rubyonrails.org/classes/ActiveRecord/ModelSchema.html#method-c-ignored_columns-3D) を使えばこのカラム自体が無視されるようになり、エラーを回避できます。

```
class User < ApplicationRecord
  self.ignored_columns = %w(class)
end
```

Rails4であれば以下で同様のことができるようです。

```
class User < ActiveRecord::Base

  def self.columns
    super.reject {|column| column.name == 'class'}
  end

end
```

## ignored_columnsで防げないパターン

ただし、`ignored_columns` でも完全に防げない場合があります。

`to_json` メソッドでエラーが発生します。

```
pry(main)> User.all.to_json
SystemStackError: stack level too deep
```

この場合は、`except` オプションで回避できます。

```
User.all.to_json(except: [:class])
```

今回の場合は、classカラム自体が不要だったので `ignore_columns` で対応しました。
classを使いたい場合は、どのように回避すればいいんでしょうか。。。

どなたか知っている人がいれば情報をいただきたいです。

