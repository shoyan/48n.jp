---
layout: post
title: "knife zeroを使ってレシピを適用する"
date: 2016-05-10 13:29:15 +0900
comments: true
categories: chef
description: "Chefでよく使われるknifeコマンドですが、そのプラグインであるknife zeroを使ってレシピを適用する方法を紹介します。knife zeroはknifeプラグインで、リモートnode上でchef-clientを実行するツールです。"
---

Chefでよく使われるknifeコマンドですが、そのプラグインである[knife zero](https://github.com/higanworks/knife-zero)を使ってレシピを適用する方法を紹介します。  
knife zeroはknifeプラグインで、リモートnode上でchef-clientを実行するツールです。

リモートnodeとはchefを適用するサーバー(管理対象となるサーバー)のことです。

## インストール

今回はGemfileに定義してインストールします。


```
# Gemfile
gem 'knife-zero'

```

以下でインストールされます。


```
$ bundle

```

## chefをリモートnodeにインストール

まずはchefをリモートnodeにインストールします。  
以下のコマンドでインストールします。


```
$ bundle exec knife zero bootstrap shoyan@server01.example.com --sudo

```

実行すると `node/` 配下にファイルが作成さます。

`chef_environment`と`run_list`を追加します。


```
{
  "name": "server01.example.com",
  "chef_environment": "production",
  "run_list": [
    “role[awesome_cookbook]"
  ],
  "normal": {
    "knife_zero": {
      "host": "server01.example.com"
    },
    "tags": [

    ]
  },
  "automatic": {
    "fqdn": "server01.example.com",
    "os": "linux",
    "os_version": "2.6.32-504.3.3.el6.x86_64",
    "platform": "centos",
    "platform_version": "6.4",
    "hostname": "server01.example.com",
    "ipaddress": “192.168.1.1",
    "roles": [

    ]
  }
}

```

リモートnodeにログインして、chefのコマンドが実行されていることを確認してみます。


```
$ ssh shoyan@server01.example.com

[shoyan@server01 ~]$ chef-[Tabを押す]
chef-apply   chef-client  chef-shell   chef-solo

```

## レシピをリモートnodeに適用する

Chefを実行する準備ができました。
以下のコマンドでレシピをリモートnodeに適用します。


```
$ bundle exec knife zero converge 'fqdn:server01.example.com' -x shoyan

```

