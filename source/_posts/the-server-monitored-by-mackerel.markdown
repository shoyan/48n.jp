---
layout: post
title: "マカレルでサーバーを監視する"
date: 2016-05-19 14:16:15 +0900
comments: true
categories: Chef
description: "サーバーの監視にマカレルを導入してみました。マカレルでサーバーを監視をするには監視対象のサーバーにマカレルエージェントをインストールする必要があります。Chefでのマカレルエージェントのインストールと設定方法を紹介します。"
---

サーバーの監視に[マカレル](https://mackerel.io/ja/features)を導入してみました。  
使ってみた感想としては、難しい設定もなく簡単に導入することができて、とてもよくできているなぁと関心しました。  

## マカレルエージェントのインストール

マカレルでサーバーを監視をするには監視対象のサーバーにマカレルエージェントをインストールする必要があります。
また、事前に申し込みをして、apikeyを発行しておく必要があります。

マカレルエージェントのインストールはChefを使って行います。  
[cookbook-mackerel-agent](https://github.com/mackerelio/cookbook-mackerel-agent)を使いました。

metadata.rb

```
depends 'mackerel-agent'

```

recipes/default.rb

```
include_recipe 'mackerel-agent'
include_recipe 'mackerel-agent::plugins'
yum_package 'mackerel-check-plugins'

```

cookbook-mackerel-agentだけだとcheckプラグインが入らなかったので`yum_package`リソースを使ってインストールしています。  

attributes/default.rb

```
default['mackerel-agent']['package-action'] = 'upgrade'
default['mackerel-agent']['conf']['apikey'] = ‘API KEYをかく'
default['mackerel-agent']['conf']['plugins'] = true

```

サーバーにレシピを適用するとマカレルの管理画面にサーバーがでてくるのでロールを設定します。  
これでサーバーのメトリクスを見ることができます。

## プラグインを使う

マカレルには様々なプラグインが用意されています。  
使い方はgithubのREADMEに書いてあります。  
https://github.com/mackerelio/mackerel-agent-plugins

自分はプラグインを使って以下のことを行っています。

### linuxマカレルプラグインを使って様々なメトリクスをだす

linuxマカレルプラグインを使えばswapやnetstat、Disk read time 等のグラフを表示することができます。

attributes/default.rb に以下を定義します。


```
default['mackerel-agent']['conf']['plugin.metrics.linux']['command'] = '/usr/local/bin/mackerel-plugin-linux'

```

### Unicornマカレルプラグインを使ってUnicornのメトリクスをだす

Unicornマカレルプラグインを使えばダッシュボードにUnicornのメモリとワーカ数のグラフが表示されます。

attributes/default.rb に以下を定義します。


```
default['mackerel-agent']['conf']['plugin.metrics.unicorn']['command'] = "/usr/local/bin/mackerel-plugin-unicorn -pidfile=/var/www/app/shared/tmp/pids/unicorn.pid"

```

### httpの監視

httpのレスポンスをチェックして監視を行います。  
細かい設定を行いたい場合は、`check-tcp`がよいですが、単純なチェックでよいのであれば`http`のほうが設定が簡単です。

attributes/default.rb に以下を定義します。


```
default['mackerel-agent']['conf']['plugin.checks.http']['command'] = "/usr/bin/check-http -u http://localhost"

```

### ログファイルの監視

nginxのログファイルを監視し、500系のエラーが頻発したときは通知するようにします。

attributes/default.rb に以下を定義します。


```
/usr/bin/check-log --file /var/log/nginx/access.log --pattern 'HTTP/1\.[01]" [5][0-9][0-9] ' --warning-over 3 --critical-over 10 --return

```

## 監視ルールの設定

監視ルールを設定することで閾値を超えた際に通知をすることができます。  
CPU、メモリ、ロードアベレージ、ディスク容量等の監視を管理画面より設定できます。  
詳しい設定方法については以下を参照ください。  

* [監視・通知を設定する - Mackerel ヘルプ](https://mackerel.io/ja/docs/entry/howto/alerts)

## Slackに通知する

何か異常が起きた場合はSlackに通知をするようにします。  
こちらも管理画面から設定できます。  
具体的な設定方法については、ドキュメントを参照ください。  

* [監視・通知を設定する - Mackerel ヘルプ](https://mackerel.io/ja/docs/entry/howto/alerts)

## 参考リンク
* [ヘルプ - Mackerel ヘルプ](https://mackerel.io/ja/docs/)
