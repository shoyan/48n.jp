---
layout: post
title: "JekyllテーマのminimaをAMP対応した"
date: 2017-04-14 15:11:53 +0900
comments: true
categories: jekyll
description: "JekyllテーマのminimaをAMP対応しました。デモサイトも用意していてこちらで確認できます。"
---

こんにちは、SHOYANです。

JekyllテーマのminimaをAMP対応しました。

* https://github.com/shoyan/aminima

名前は AMP + Minima = AMinima です。

デモサイトも用意していてこちらで確認できます。

* https://shoyan.github.io/aminima/

## Minimaとは

MinimaはJekyllのテーマです。
jekyll new で作成した場合にデフォルトでインストールされるテーマです。
URLはこちらです。

* https://github.com/jekyll/minima

## どのようにAMP対応したのか

どのようにAMP対応をしたのかを説明します。
わりとあっさりできました。

まず、ampを宣言します。

```html
<html amp>
```

そして、charsetを定義します。

```html
<meta charset="utf-8">
```

次にampランタイムをロードします。

```html
<script async src="https://cdn.ampproject.org/v0.js"></script>
```

また、AMPはcanonicalの宣言が必要なので定義します。

```html
<link rel="canonical" href="https://shoyan.github.io/aminima/2016/05/20/welcome-to-jekyll.html">
```

CSSはインラインで定義しないといけないのでインラインで定義します。

```html
<style amp-custom
  h1 {
    color: red;
  }
</style
```

次にAMP boilerplateを定義します。

```html
<style amp-boilerplatebody{-webkit-animation:-amp-start 8s steps(1,end) 0s 1 normal both;-moz-animation:-amp-start 8s steps(1,end) 0s 1 normal both;-ms-animation:-amp-start 8s steps(1,end) 0s 1 normal both;animation:-amp-start 8s steps(1,end) 0s 1 normal both}@-webkit-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@-moz-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@-ms-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@-o-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}</style<noscript<style amp-boilerplatebody{-webkit-animation:none;-moz-animation:none;-ms-animation:none;animation:none}</style</noscript
```

これでAMPの基本設定はOKです。

実際のコードはリポジトリを参照してみてください。

- https://github.com/shoyan/aminima/blob/master/_includes/head.html

### amp-analyticsを使う

通常のAnalyticsのタグはAMPでは動かないのでamp-analyticsを使います。
amp-analyticsはAMP用に用意されたコンポーネントです。

- https://ampbyexample.com/components/amp-analytics/

テーマのソースコードはこの辺りです。

- https://github.com/shoyan/aminima/blob/master/_includes/google-analytics.html

## Aminimaのインストール方法

Jekyllをインストールしていない場合はjekyllをインストールしてください。

```
$ gem install jekyll
```

ブログを作成します。

```
$ jekyll new blog

```

Gemfileを編集します。

```
以下のように書き換える

gem "minima", "~> 2.0"
↓
gem "aminima"
```

`_config.yml` のthemeを編集します。

```
theme: aminima
```

bundleコマンドを実行します。

```
$ bundle
```

サーバーを立ち上げます。

```
$ jekyll server
```

ブラウザで http://localhost:4000 にアクセスすればサイトが表示されます。

動かない部分や不明な点はコメント欄や[Twitter](https://twitter.com/sinn_shoyan)などでお気軽にご連絡ください！
