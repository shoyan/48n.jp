---
layout: post
title: "Exception Notificationでundefined method `current' for Time:Classエラーがでた"
date: 2016-05-13 13:28:11 +0900
comments: true
categories: sinatra ruby
description: "例外発生時にException Notificationで通知をしようと思い、導入してみたところTime.currentがundefined methodだというエラーがでました。active_supportを読みこむことでSinatraで使えるようにしました。"
---

例外発生時に[Exception Notification](https://github.com/smartinez87/exception_notification)で通知をしようと思い、導入してみたところ以下のエラーがでました。


```
ERROR: Failed to generate exception summary:
ActionView::Template::Error: undefined method `current' for Time:Class

```

日付の取得に`Time.current`を使っており、`Time.current`はActive supportにより拡張されたメソッドなのでActive Supportを使っている環境でしか動作しません(要するにRailsじゃないと動かない。Sinatraは...)。

PRもでているので対応してほしいところです。

* [Fix timestamp error](https://github.com/smartinez87/exception_notification/pull/332)

## 対応方法

`require 'active_support/core_ext/time’` をすることで`Time.current`を使えるようにしました。

サンプルコード


```
require 'rubygems'
require 'bundler/setup'
require "sinatra/base"
require 'exception_notification'
# Time.currentを使えるようにする
require 'active_support/core_ext/time'

class App < Sinatra::Application
  use ExceptionNotification::Rack,
    :email => {
      :email_prefix => "[Exception] ",
      :sender_address => %{"notifier" <notifier@example.com>},
      :exception_recipients => %w{shoyan@example.com}
    }

  get '/' do
    begin
      // 何かの処理
    rescue Exception => e
      status 500
      ExceptionNotifier.notify_exception(e, env: env)
      e.message
    end
  end
end

```
