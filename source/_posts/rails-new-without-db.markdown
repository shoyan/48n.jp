---
layout: post
title: "RailsをDBなしで初期化する方法"
date: 2024-02-08 10:00:00
updated: 2024-02-08 10:00:00
comments: true
category: 技術記事
tags:
  - Ruby
  - Rails
  - チュートリアル
  - 開発環境
---

RailsはデフォルトではDBを利用する設定になっているので、普通にrails newすると、Gemfileにsqlite3が定義されたりdatabase.ymlが作成されてしまいます。

DBなしでRailsを使いたい場合は、以下のコマンドを実行します。


```bash
$ rails new application_name -O

```
