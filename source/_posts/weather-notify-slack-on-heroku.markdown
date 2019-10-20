---
layout: post
title: "天気予報をSlackに通知する on Heroku"
date: 2015-10-06 16:12:17 +0900
comments: true
categories: slack heroku ruby
---

[@keita_kawamoto](https://twitter.com/keita_kawamoto)が天気予報を見ずに出社して、途中で雨に降られて困っていたので天気予報通知をつくってみました。  
Herokuでスケジューラーに登録してSlackに通知するようにしています。

![weather-nitify-slack](/images/weather-notify-slack.png)

[ソースコード](https://github.com/shoyan/slack-weather-notifier)は公開しているので参考にどうぞ。

## 天気情報を取得する

天気情報の取得は[weather_hacks](http://weather.livedoor.com/weather_hacks/webservice)のAPIを利用しました。  
APIはjsonでレスポンスが返されます。  

以下、サンプルコードです。  

slack-weather-notifier.rb

```ruby
require 'json'
require 'open-uri'

uri = 'http://weather.livedoor.com/forecast/webservice/json/v1?city=400010'

res     = JSON.load(open(uri).read)
title   = res['title']
link    = res['link']
weather = res['forecasts'].first
message = "[#{weather['date']}の#{title}](#{link})は「#{weather['telop']}」です。"
puts message

```

ターミナルで`ruby slack-weather-notifier.rb` と実行すると、今日の天気の情報が表示されます。

## Slackに通知する

天気情報の取得ができたので、次はSlackに通知します。  
Slackの通知にはIncoming WebHooksを使います。  
Incoming WebHooksを使うには、Webhook URLの発行が必要です。  

Slackの設定画面にアクセスします。  
`https://example.slack.com/services/new/incoming-webhook`  
`example.slack.com`は自分のSlack Teamのドメインをいれてください。  

通知先のチャンネルを選んで、Add Incoming Webhooks Integrationのボタンを押すと発行されます。

![slack-setting-example.png](/images/slack-setting-example.png)

設定画面でWebhook URLが確認できます。  
このURLにPOSTすると、Slackに通知できるようになります。  

Customize NameやCustomize Iconを変更すると、通知するbotの名前やアイコンが変更できます。 

![slack-setting-example2.png](/images/slack-setting-example2.png)

では、通知をしてみましょう。  
通知は[slack-incoming-webhooks](https://github.com/shoyan/slack-incoming-webhooks)というgemを使うと簡単にできるので、今回はそれを使います。  

### slack-incoming-webhooks をインストール

```
$ gem install slack-incoming-webhooks

```

先ほどのスクリプトにslack通知の設定を追加します。  

```ruby
require 'json'
require 'open-uri'
require 'slack/incoming/webhooks'

uri = 'http://weather.livedoor.com/forecast/webservice/json/v1?city=400010'

res     = JSON.load(open(uri).read)
title   = res['title']
link    = res['link']
weather = res['forecasts'].first

slack = Slack::Incoming::Webhooks.new "webhook_url"
slack.post "<#{link}|#{weather['date']}の#{title}>は「#{weather['telop']}」です。"

```

ターミナルで`ruby slack-weather-notifier.rb` と実行してみましょう。  
Slackに通知されれば成功です。  

## HerokuでAppを作成
さて、天気情報とSlack通知ができるようになりました。  
これが定期的に実行できれば便利ですね。  

Herokuを使えば無料でスクリプトを定期実行できます。  
Herokuでアカウントを作成します。  
https://signup.heroku.com/

アカウント登録をしたら、Toolbeltをインストールします。  
Toolbeltは、Herokuをコマンドラインから利用できるようになるツールです。  
https://toolbelt.heroku.com/

次に認証を行います。  
ターミナルに`heroku login`とコマンドを入力します。  
メールアドレスとパスワードが聞かれるので、先ほどHerokuで登録したメールアドレスとパスワードを入力してください。  

## アプリを作成
アプリケーションを登録しましょう。

Herokuにデプロイするには、Gitを使ってリモートリポジトリへプッシュする必要があるので、Gitの登録を行います。

ここでは、slack-weather-notifierというディレクトリを作成し、そこに先ほどのファイルを作成し、Gitで管理します。

また、Herokuでslack-incoming-webhooks gemを使うためにGemfileでgemを管理します。  
slack-weather-notifierディレクトリの直下にGemfileを作成します。  

Gemfile

```
source 'https://rubygems.org'

gem 'slack-incoming-webhooks'

```

ターミナルで `bundle install`と実行すると、gemがインストールされ、Gemfile.lockファイルが作成されます。

もし、bundlerをインストールしていない場合は、 `gem install bundler` でインストールしてください。

以下のような構成になります。

```
slack-weather-notifier
├── Gemfile
├── Gemfile.lock
└── slack-weather-notifier.rb

```

Webhook Urlは外部に公開すべきではないので、環境変数に登録して、それを使うようにします。



```ruby
require 'json'
require 'open-uri'
require 'slack-incoming-webhooks'

uri = 'http://weather.livedoor.com/forecast/webservice/json/v1?city=400010'

res     = JSON.load(open(uri).read)
title   = res['title']
link    = res['link']
weather = res['forecasts'].first

slack = Slack::Incoming::Webhooks.new ENV['WEBHOOK_URL']
slack.post "<#{link}|#{weather['date']}の#{title}>は「#{weather['telop']}」です。"

```


Gitに登録しましょう。  

```
$ git init
$ git add .
$ git commit -m "first commit"

```

Herokuにpushします。

```
$ git push heroku master

```
WEBHOOK URLを登録します。

```bash
heroku config:set WEBHOOK_URL=ここにwebhook_urlを入力

```

## スケジューラーを登録
### カード登録
スケジューラーを利用するにはカード登録が必要です。  
無料利用枠を超過した場合は料金が発生しますが、数秒で終わるスクリプトなので大丈夫です。


以下でカードを登録できます。  
https://dashboard.heroku.com/account/billing

### スケジューラーを登録
カード登録ができたらAdd-onsにスケジューラーを追加します。

以下のコマンドでコンソールから登録できます。

```
heroku addons:add scheduler:standard

```

またはaddonページから登録してください。  
https://addons.heroku.com/scheduler

### スケジュール管理画面を開く
https://heroku-scheduler.herokuapp.com/dashboard

コンソールからも開けます。

```
$ heroku addons:open scheduler

```

### スケジューラーの設定
Select an appで登録したアプリケーションを選び 'Add Standard for free' のボタンをクリックすると登録できます。  
登録したら、ダッシュボードのアプリケーションのページのAdd-onsにHeroku Schedulerが表示されています。  

クリックすると、設定ページに飛ぶので、コマンドと時間を登録します。  
ちなみに時間はUTCなので気をつけてください。  
0:00で登録すると日本時間の9:00に通知されます。

![heroku-scheduler](/images/heroku-scheduler.png)

[ソースコード](https://github.com/shoyan/slack-weather-notifier)は公開しているので参考にどうぞ。
