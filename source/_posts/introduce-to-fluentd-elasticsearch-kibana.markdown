---
layout: post
title: "Fluentd、ElasticsearchとKibanaでログ検索とグラフ表示を可能にする"
date: 2016-07-05 17:21:32 +0900
comments: true
categories: fluentd
description: "Fluentdで転送されたログをElasticsearchに登録し、Kibanaでログ検索とグラフ表示する方法を紹介します。今回はsyslogのログをFluentd経由でElasticsearchに登録し、Kibanaで表示します。"
---

[前回の記事](/blog/2016/07/04/fluentd-tutorial/)で[Fluentd](http://www.fluentd.org/)を使ってログをログ収集サーバに転送する方法を紹介しました。
今回は転送されたログを[Elasticsearch](https://www.elastic.co/jp/products/elasticsearch)に登録し、[Kibana](https://www.elastic.co/jp/products/kibana)でログ検索とグラフ表示する方法を紹介します。

ログ収集サーバー(前回の記事でいうVagrant)の変更を行っていきます。

## FluentdのElasticsearchプラグインをインストール

まずは、FluetndのElasticsearchプラグインをインストールしておきます。

```
$ sudo /usr/sbin/td-agent-gem install fluent-plugin-elasticsearch
```

## Javaのインストール

Elasticsearchを動かすにはJavaが必要なのでJavaをインストールします。

```
$ sudo yum install java-1.7.0-openjdk
$ sudo yum install java-1.7.0-openjdk-devel
$ java -version
java version "1.7.0_101"
```

## Elasticsearchのインストールと起動

今回はアーカイブをダウンロードしてきてインストールします。
インストールといっても特に設定は不要でアーカイブを展開して`bin/elasticsearch`を実行するだけです。

```
$ curl -O https://download.elastic.co/elasticsearch/release/org/elasticsearch/distribution/tar/elasticsearch/2.3.3/elasticsearch-2.3.3.tar.gz
$ cd elastic search-2.3.3
$ ./bin/elasticsearch
```

## kibanaのインストールと起動

Kibanaも同じようにアーカイブをダウンロードして起動します。

```
$ curl -O https://download.elastic.co/kibana/kibana/kibana-4.5.1-linux-x64.tar.gz
$ cd kibana-4.5.1-linux-x64
$ ./bin/kibana
```

`http://192.168.33.10:5601` にブラウザでアクセスするとKibanaの管理画面が表示されます。
Indexの設定が必要ですが、ここでは何もせず次に進みます。

## syslogのログをElasticsearchに格納する

syslogのログをElasticsearchに格納し、Kibanaで参照します。

`/etc/td-agent/td-agent.conf`に以下を追記します。

```
<source>
  @type syslog # syslogプラグイン
  port 42185   # portは42185を指定
  tag syslog   # syslogというtagをつける
</source>

<match syslog.**>
  @type elasticsearch # elasticsearchプラグインを使う
  logstash_format true # ログのフォーマット
  flush_interval 10s # 10秒ごとに登録する
</match>
```

fluendをリスタートします。

```
$ sudo service td-agent restart
```

次にsyslogの設定をします。

`/etc/rsyslog.conf` に以下を追記します。
syslogが出力するログをFlutedがlistenするポート、42185に転送するための設定です。

```
*.* @127.0.0.1:42185
```

設定を反映させるため、syslogをリスタートします。

```
$ sudo /etc/init.d/rsyslog restart
```

先ほど確認した `http://192.168.33.10:5601` に戻ります。
syslogの出力がElasticsearchに登録されると、Indexを登録できるようになります。
Indexは`logstash-2016.07.05` のような名前で作成されます。
セレクトボックスからIndexを選択し、Createボタンを押下後にKibanaでログの表示、検索が行えます。

手動でElasticsearchにログを送りたい場合は以下のコマンドで登録できます。

```
$ logger -t test foobar
```

もしくは、Fluentdのhttpポートにリクエストします。

```
$ curl -X POST -d 'json={"json":"Hello"}' http://localhost:8888/syslog.test
```

## 参考リンク

- [Free Alternative to Splunk Using Fluentd](http://docs.fluentd.org/articles/free-alternative-to-splunk-by-fluentd)

