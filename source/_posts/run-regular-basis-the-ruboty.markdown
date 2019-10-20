---
layout: post
title: "Slackで定期的にRubotyに仕事をさせる"
date: 2016-10-31 16:58:56 +0900
comments: true
categories: ruby chatops
description: "ChatOpsの一環としてSlackにRubotyを常駐させているのだが、Rubotyに定期的にタスクを実行させたいことがあった。cronを使った方法を紹介する。"
---

ChatOpsの一環としてSlackにRubotyを常駐させているのだが、Rubotyに定期的にタスクを実行させたいことがあった。
最初はruby-cronを使ってみたのだが、Slackだとうまく動かなかった。
具体的にはジョブの登録はできるのだがジョブの実行(通知)がされない。

うまくいかなかったのでSlackのリマインダー機能を使ってみることにした。
Slackのリマインダー機能を使うと `Reminder:` と言うプレフィックスが入るのでRubotyが反応せず、こちらもうまくいかなかった。

最終的にはcronでスクリプトを定期実行させることにした。
Slackに通知するスクリプトを作って、それをcronで定期的に実行させる。
メッセージを拾ったRubotyがタスクを実行するといった感じだ。

### サンプルコード

```ruby
require "faraday"

username = 'your name'
channel = 'your channel'
slack_token = 'your slack token'
icon_url = 'your icon url'

body = {
  username: username,
  channel: channel,
  icon_url: icon_url,
  text: "ruboty ping"
}

Faraday.new('https://slack.com') do |c|
  c.request :url_encoded
  c.adapter Faraday.default_adapter
end.post("/api/chat.postMessage?token=#{slack_token}", body)
```

これでRubotyに定期的に仕事をさせることができるようになった。
