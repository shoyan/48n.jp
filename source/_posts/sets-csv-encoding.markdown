---
layout: post
title: "[Ruby]CSVファイルのエンコードをsjisに指定する"
date: 2016-06-20 13:23:28 +0900
comments: true
categories: ruby
description: "日本語の場合、CSVのファイルエンコードをShift JISにする要件がけっこうあると思います。RubyのCSVライブラリは、encodingというオプションが用意されており、encoding: ’sjis' のようにファイルエンコーディングを指定できます。"
---

日本語の場合、CSVのファイルエンコードをShift JISにする要件がけっこうあると思います。  
RubyのCSVライブラリは、`encoding`というオプションが用意されており、`encoding: ’sjis’` のようにファイルエンコーディングを指定できます。


```ruby
require 'csv'
CSV.open("hoge.csv", "wb", encoding: 'sjis') do |csv|
  csv << ["ID", "担当者", "メールアドレス"]
end

```

nkfコマンドを使ってファイルエンコーディングを確認します。


```
⇒  nkf -g hoge.csv
Shift_JIS

```

Shift_JISで作成されていることが確認できました。
