---
layout: post
title: "Fluentdを使ってローカル環境にログ収集サーバを構築する"
date: 2016-07-04 13:50:35 +0900
comments: true
categories: Fluentd
description: "アプリケーションサーバとログ収集サーバにFluentdをインストールし、アプリケーションサーバからログ収集サーバにログをフォワードする方法を紹介します。ここでは、アプリケーションサーバをMacOS X、ログ収集サーバをVagrantとします。"
---

アプリケーションサーバとログ収集サーバにFluentdをインストールし、アプリケーションサーバからログ収集サーバにログをフォワードする方法を紹介します。
ここでは、アプリケーションサーバをMacOS X、ログ収集サーバをVagrantとします。

## Fluentdをホストマシン(Mac)にインストール

.dmgをダウンロードしてインストールしてください。

* [ダウンロード](https://td-agent-package-browser.herokuapp.com/2/macosx)

以下でFluentdを起動します。

```
$ sudo launchctl load /Library/LaunchDaemons/td-agent.plist
```

Fluentdのログは `/var/log/td-agent/td-agent.log` に出力されます。

```
$ less /var/log/td-agent/td-agent.log
```

停止は以下です。

```
$ sudo launchctl unload /Library/LaunchDaemons/td-agent.plist
```

設定ファイルは `/etc/td-agent/td-agent.conf` です。
設定を反映させるには、以下のコマンドで行います。

```
$ sudo launchctl stop td-agent
$ sudo launchctl start td-agent
```

以下でFluentdにリクエストします。

```
$ curl -X POST -d 'json={"json":"message"}' http://localhost:8888/debug.test
```

`/var/log/td-agent/td-agent.log` にログが出力されているはずです。

```
$ tail -n 1 /var/log/td-agent/td-agent.log
2016-07-01 16:51:47 -0700 debug.test: {"json":"message"}
```

## ログ収集サーバにFluentdをインストール

次にログ収集サーバにFluentdをインストールします。
OSはCentos6.5を使いました。
vagrantの使い方は割愛します。
networkはprivate_networkとし、192.168.33.10でアクセスできることとします。

以下でログ収集サーバにログインします。

```
$ vagrant ssh

# iptablesは無効にしておきます
$ sudo service iptables stop

# 以下のコマンドでFluentdインストールします。
$ curl -L https://toolbelt.treasuredata.com/sh/install-redhat-td-agent2.sh | sh

# Fluentdを起動します
$ sudo /etc/init.d/td-agent start
```

ログ収集サーバでも同じように確認してみます。

```
$ curl -X POST -d 'json={"json":"message"}' http://localhost:8888/debug.test
$ tail -n 1 /var/log/td-agent/td-agent.log
2016-07-01 16:51:47 -0700 debug.test: {"json":"message"}
```

## アプリケーションサーバからログ収集サーバのFluentdにリクエストを送る

アプリケーションサーバからログ収集サーバのFluentdにリクエストを送れるかを確認します。

```
$ curl -X POST -d 'json={"json":"message"}' http://192.168.33.10:8888/debug.test
```

ログ収集サーバでログを確認します。
Fluentdは出力をバッファするのでフォワードされるまでにタイムログがある場合があります。

```
$ tail -n 1 /var/log/td-agent/td-agent.log
```

疎通を確認できたら、アプリケーションサーバからログ収集サーバのFluentdにログをforwardしてみましょう。

アプリケーションサーバの`/etc/td-agent/td-agent.conf` を以下のように編集します。

```
<source>
  type http
  port 8888
</source>

<source>
  @type forward
  port 24224
</source>

# tagがvagrantの場合はvagrantのFluentdにフォワードする
<match tag vagrant.**>
  type forward
  <server>
    host 192.168.33.10
    port 24224
  </server>
</match>

<match debug.**>
  type stdout
</match>
```

ログ収集サーバの`/etc/td-agent/td-agent.conf`に以下の設定を追加します。
tagがvagrantで送られてきたものをログに出力します。

```
<match vagrant.**>
  type stdout
</match>
```

以下のコマンドでログ収集サーバのFluentdにデータがフォワードされます。

```
$ curl -X POST -d 'json={"json":"Fluentd!!!"}' http://localhost:8888/vagrant.test
```

以上で基本的なフォワードの設定ができました。

## ファイルを監視してログ収集サーバにフォワードする

次はファイルを監視してファイルにデータが追加されたらその内容をログ収集サーバにフォワードするようにします。
`in_tail`プラグインを使います。
`in_tail`プラグインを使うにはtypeにtailを使います。

アプリケーションサーバの`/etc/td-agent/td-agent.conf`に以下を追記します。

```
<source>
  type tail # in_tailプラグインを指定
  path /tmp/access_log # 監視するファイルの指定
  tag vagrant.access_log # ログにつけるタグを指定
  pos_file /tmp/access_log.pos # ファイル内のどの行までを読んだかを記録しておくファイルを指定
  format none # フォーマットはnoneを指定
</source>
```

設定を反映させるには、以下のコマンドで行います。

```
$ sudo launchctl stop td-agent
$ sudo launchctl start td-agent
```

`/tmp/access_log` に追記してみましょう。

```
$ echo "hello" >> /tmp/access_log
```

すると、ログ収集サーバのFluentdに内容がフォワードされます。

参考リンク

- [柔軟なログ収集を可能にする「Fluentd」入門](http://knowledge.sakura.ad.jp/tech/1336/)
- [Installing Fluentd using .dmg Installer (MacOS X)](http://docs.fluentd.org/articles/install-by-dmg)
- [Configuration File](http://docs.Fluentd.org/articles/config-file)
