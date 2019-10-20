---
layout: post
title: "CircleCIでお手軽にCI環境をつくる"
date: 2016-07-22 14:12:06 +0900
comments: true
categories: 
description: "最近はCIプラットフォームが充実していて、Travice CI、Wercker、Droneなど様々なプラットフォームが開発されています。最近はCirlceCIを使っているのですが、なかなか便利です。CircleCIの便利な点と注意点を紹介します。"
---

最近はCIプラットフォームが充実していて、Travice CI、Wercker、Droneなど様々なプラットフォームが開発されています。
最近は[CircleCI](https://circleci.com/)を使っているのですが、なかなか便利です。

## CircleCIの便利な点

CircleCIの便利な点は特別な設定をせずにテストコードさえあればCIができてしまうことです。
CircleCIにアカウントを作って、CIを行うリポジトリを設定すればOKです。
リポジトリにpushするとCIが実行されます。
ソースコードからbundle installなどの必要な前処理をしてくれるので、CIを行うための前準備的な設定が必要ありません。

私は[git_find_commiter](https://github.com/shoyan/git_find_committer)や[php-syntax-check](https://github.com/shoyan/php-syntax-check)でCircleCIを使っています。

## 注意点

デフォルトのRubyのバージョンが古いので(マニュアルによるとUbuntu 12.04 and Ubuntu 14.04 build imagesのもの)、Ruby1.9から導入されたキーワード引数など比較的新しく導入された機構を使うと構文エラーが発生することがあります。
その場合は`circle.yml`ファイルにRubyのバージョンを設定できます。

```
machine:
  ruby:
    version: 2.3.0
```

また、bundlerのバージョンでエラーが発生することがありました。
その場合は、以下のようにバージョンを指定することができます。

```
dependencies:
  pre:
    - gem install bundler -v 1.12.5
```

circle.ymlの構文についてはドキュメントを参考にしてください。

- [Configuring CircleCI - CircleCI](https://circleci.com/docs/configuration/)
