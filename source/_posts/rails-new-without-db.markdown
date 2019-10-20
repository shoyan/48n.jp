---
layout: post
title: "RailsをDBなしで使う"
date: 2015-06-29 15:12:27 +0900
comments: true
categories: 
---

RailsはデフォルトではDBを利用する設定になっているので、普通にrails newすると、Gemfileにsqlite3が定義されたりdatabase.ymlが作成されてしまいます。

DBなしでRailsを使いたい場合は、以下のコマンドを実行します。


```bash
$ rails new application_name -O

```
