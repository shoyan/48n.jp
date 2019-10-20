---
layout: post
title: "Rubyのモジュールはあと勝ち"
date: 2017-05-10 16:52:23 +0900
comments: true
categories: ruby rails
description: "Rubyのモジュールはあと勝ちということを知りました。もう少し正確に言うと、異なるモジュールに同じ名前のメソッドが定義してあった場合、後にinclude されたメソッドで上書きされます。"
---

こんにちは、SHOYANです。

今回はRubyのモジュールについての話しです。
結論ファーストです。Rubyのモジュールはあと勝ちということを知りました。

もう少し正確に言うと、異なるモジュールに同じ名前のメソッドが定義してあった場合、後にinclude されたメソッドで上書きされます。
この知見はActiveRecordのソースコードを読んでいて知りました。

ActiveRecord::Validationsモジュールのソースコードを読んでいて、ActiveRecord::Validationsモジュールにsaveメソッドがあるのが気になりました。
というのも、saveメソッドはActiveRecord::Persistentモジュールにも定義してあるからです。

ActiveRecord::ValidationsモジュールのAPIのドキュメントによると、以下のように書いてあります。

> The regular ActiveRecord::Base#save method is replaced with this when the validations module is mixed in, which it is by default.

訳: 通常、ActiveRecord::Base#メソッドはバリデーションモジュールが混在している場合にはこれと置き換えられます。これはデフォルトです。

ここでActiveRecordの仕組みについて少し説明しておくと、ActiveRecord::Baseというクラスがあり、そのクラスで各モジュールをincludeしています。

2017年5月現在では、以下のようにincludeされています。

```ruby
module ActiveRecord
  class Base
    include 様々なモジュール
    include Persistence
    include 様々なモジュール
    include Validations
    include 様々なモジュール
  end
end
```

* https://github.com/rails/rails/blob/master/activerecord/lib/active_record/base.rb

ActiveRecord::Persistenceの後にActiveRecord::Validationsモジュールがincludeされています。
どうやら後にincludeされたモジュールのメソッドで上書きされているようです。

ここでサンプルコードを使って確かめてみました。

```ruby
module Hoge
  def name
    'hoge'
  end
end

module Moge
  def name
    'moge'
  end
end

class Man
  include Hoge
  include Moge
end

man = Man.new
p man.name
```

実行してみると、`moge` が出力されました。
予想通り、後から読み込まれたモジュールのメソッドで上書きされていました。

メソッド名が重複するということは普通にありそうなので、この挙動については知っておいたほうがいいかもしれませんね。
