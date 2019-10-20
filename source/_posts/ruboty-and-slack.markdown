---
layout: post
title: "Botsインテグレーションを使って手軽にRubotyをSlackで動かす"
date: 2016-08-02 13:40:25 +0900
comments: true
categories: ruby gem chatops
description: "RubotyにはSlackアダプターが用意してありますが、新しくユーザーの作成が必要です。今回はユーザーの作成が必要のない、Botsインテグレーションとruboty-slack_rtmを使ってRubotyをSlackで動かす方法を紹介します。"
---

前回は[Rubotyのインストールとプラグインチュートリアル](/blog/2016/07/29/first-step-ruboty/)でRubotyの導入方法を紹介しました。
今回はRubotyをSlackで動かす方法を紹介します。

RubotyにはSlackアダプターが用意してありますが、新しくユーザーの作成が必要です。
ユーザーの作成が必要のない、Botsインテグレーションと[ruboty-slack_rtm](https://github.com/rosylilly/ruboty-slack_rtm)を使ってRubotyをSlackで動かす方法を紹介します。

## ruboty-slack_rtmのインストール

Gemfileに以下を定義します。

```
gem "ruboty-slack_rtm"
```

`bundle install` コマンドでインストールします。

## SlackのBotsインテグレーションを設定する

Slackの管理画面でBotsインテグレーションを登録します。

![bots-integration](/images/bots-integration.png)

登録するとTokenが発行されるので、そのTokenをSLACK_TOKENという名前で環境変数に設定します。
Rubotyは dotenv に対応しているので、`.env` ファイルを作成し、そこにTOKENを登録しておくと便利です。

.env

```
SLACK_TOKEN=<slack token>
```

Ruboty を起動します。`.env` ファイルを読み込むように `—dotenv` オプションをつけて起動します。

```
bundle exec ruby --dotenv
```

起動すると SlackのRubotyがログインマークに変わります。

あとは、チャンネルにinviteすればSlackでRubotyが使えます！
ちなみにrubotyではなく、Botsインテグレーションで登録したユーザー名に反応します。

### 関連記事

* [Rubotyのインストールとプラグインチュートリアル](/blog/2016/07/29/first-step-ruboty/)
