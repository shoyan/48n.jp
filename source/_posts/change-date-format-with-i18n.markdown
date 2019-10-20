---
layout: post
title: "Railsで日付をいい感じ(スラッシュ区切り)に表示する"
date: 2016-11-10 18:28:31 +0900
comments: true
categories: rails
description: "日付は日本だと/(スラッシュ)区切りが一般的なのでそのように表示したい。I18nを使った方法を紹介する。"
---

日付は日本だと `/`(スラッシュ)区切りが一般的なのでそのように表示したい。
しかし、RailsでDateオブジェクトやDateTimeオブジェクトを表示すると以下のような感じになってしまう。

```ruby
pry(main)> Date.today.to_s
=> "2016-11-10"

pry(main)> DateTime.now.to_s
=> "2016-11-10T17:47:01+09:00"
```

`strftime`メソッドで書式を指定すれば良いのだが、面倒だしイケてないように感じる。

Rails国際化APIの`I18n`にまさにというメソッドがあったので紹介する。
その名も`localize`メソッドだ。
`localize`メソッドはDateオブジェクトやDateTimeオブジェクトを現地のフォーマットに変換する。

```ruby
pry(main)> I18n.localize(Date.today)
=> "2016/11/10"

pry(main)> I18n.localize(DateTime.now)
=> "2016/11/10 18:05:04"
```

ちなみにlocalizeの省略形で `l` というエイリアスが用意されている。

```
pry(main)> I18n.l(Date.today)
=> "2016/11/10"

pry(main)> I18n.l(DateTime.now)
=> "2016/11/10 18:07:24"
```

* [Rails国際化(I18n) API Rails ガイド](http://railsguides.jp/i18n.html#%E3%83%91%E3%83%96%E3%83%AA%E3%83%83%E3%82%AFi18n-api)
