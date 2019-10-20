---
layout: post
title: "RubyでSlack通知をする"
date: 2015-09-28 12:15:42 +0900
comments: true
categories: [ruby, gem, slack]
---

[Slack](https://slack.com/)は使っていますか？  
僕は会社やプライベートはもっぱらSlackを使っています。

SlackにはAPIが備わっていて、APIを使えばSlack通知が簡単にできます。  

今回はRubyでSlackに通知する方法を紹介します。

RubyでSlack通知をするには、以下の作業が必要です。

1. webhook urlの発行
1. webhook urlに対してpostする


## 1. webhook urlの発行
まずは、webhook urlを発行します。  
`https://yourteam.slack.com/services/new/incoming-webhook` のページで発行できます。  
Post先のチャンネルを選んで、`Add Incoming Webhooks Integration`のボタンを押すと発行されます。  

![slack-setting-example](/images/slack-setting-example.png)

channelや通知するbotの名前を決めれるので、適当に決めます。  

![slack-setting-example2](/images/slack-setting-example2.png)

これでSlack側の準備は完了です。  

## 2. webhook urlに対してpostする
webhook urlの発行ができたら、Slackに通知をしてみましょう。  

今回、Slack通知には<a href="https://github.com/shoyan/slack-incoming-webhooks" target="_blank">slack-incoming-webhooks</a>というgemを使います。  


### インストール
インストールは以下のコマンドでできます。

```
gem install slack-incoming-webhooks

```

### Slackへ通知
使い方はシンプルです。


```ruby
require 'slack/incoming/webhooks'

slack = Slack::Incoming::Webhooks.new "WEBHOOK_URL"
slack.post "Hello World"

```

もし、通知先のチャンネルや通知するユーザーネームを変更したいときは指定できます。

```ruby
slack = Slack::Incoming::Webhooks.new "WEBHOOK_URL", channel: '#other-channel', username: 'monkey-bot'

# Direct message
slack = Slack::Incoming::Webhooks.new "WEBHOOK_URL", channel: '@shoyan'

```

アクセサメソッドも用意されています。

```ruby
slack.channel = '#other-channel'
slack.icon_emoji = ':ghost:'

```

## Attachmentsを使う
さて、単純な通知ができたら次はもっとリッチなフォーマットで通知をしてみましょう。  
それには[attachments](https://api.slack.com/docs/attachments)を使います。

### Example1

![slack-example](/images/slack-example.png =500x)


```ruby
attachments = [{
  title: "Ticket #1943: Can't reset my password",
  title_link: "https://groove.hq/path/to/ticket/1943",
  text: "Help! I tried to reset my password but nothing happened!",
  color: "#7CD197"
}]
slack.post "New ticket from Andrea Lee", attachments: attachments

```


### Example2

![slack-example2](/images/slack-example2.png =500x)


```ruby
attachments = [{
  text: "<https://honeybadger.io/path/to/event/|ReferenceError> - UI is not defined",
  fields: [
    {
      title: "Project",
      value: "Awesome Project",
      short: true
    },
    {
      title: "Environment",
      value: "production",
      short: true
    }
  ],
  color: "#F35A00"
}]
slack.post "", attachments: attachments

```

### Example3

![slack-example3](/images/slack-example3.png =500x)


```ruby
attachments = [{
  title: "Network traffic (kb/s)",
  title_link: "https://datadog.com/path/to/event",
  text: "How does this look? @slack-ops - Sent by Julie Dodd",
  image_url: "https://api.slack.com/img/api/attachment_example_datadog.png",
  color: "#764FA5"
}]
slack.post "", attachments: attachments

```

いかがでしたでしょうか。  
今回紹介できなかったオプションもあるので、詳細はSlackの[attachments](https://api.slack.com/docs/attachments)のドキュメントを参考にしてください。

では、よいSlack Lifeを！
