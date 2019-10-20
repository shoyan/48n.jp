---
layout: post
title: "Twitterカードをサイトに設定する手順"
date: 2017-02-25 22:50:20 +0900
comments: true
categories: ブログ作成
description: "Twitterカードはタイムラインに表示される情報をカスタマイズできる機能です。Twitterカードを設定することでタイムラインに表示される情報をよりリッチなものにすることができます。この記事ではTwitterカードを設定する手順を紹介します。"
---

![Twitter_Logo](/images/Twitter_Logo_White_On_Blue.svg)

## Twitterカードとは

<a href="https://dev.twitter.com/cards/getting-started" target="_blank">Twitterカード</a>はタイムラインに表示される情報をカスタマイズできる機能です。
Twitterカードを設定することでタイムラインに表示される情報をよりリッチなものにすることができます。
現在はSNSにより拡散されることも多いため、Twitterカードの設定をしておくとより多くの人の目にとまるようになり、記事への流入が期待できます。

Twitterカードを設定すると以下のようになります。

![introduce_twitter_card_01](/images/introduce_twitter_card_01.png)

## Twitterカードの設定

Twitterカードの設定手順を紹介します。

### タグを設定する

Twitterカードのを利用するためにはメタタグの設置が必要です。
以下は[30歳から始める数学](/blog/2015/12/01/mathematics-of-advent-calendar/)に設定しているTwitterカードのサンプルコードです。

#### Sample Code

```
<meta name="twitter:card" content="summary_large_image" />
<meta name="twitter:site" content="@sinn_shoyan" />
<meta name="twitter:title" content="30歳から始める数学" />
<meta name="twitter:description" content="とあることから、30歳にして数学を学び始めました。いまは毎日楽しく数学の書籍を読んだり方程式を解いたりしています。本記事では、僕と同じようにもう一度数学を学びたいなと思っている人向けに、数学の魅力を再発見する方法を紹介します。" />
<meta name="twitter:image" content="http://48n.jp/images/math-1547018_640.jpg" />
```

Twitterカードのメタタグのプロパティは以下となります。

プロパティ | 必須 | 説明
---- | ---- | ----
twitter:card | Yes | twitterカードの種類を設定します
twitter:site | No | twitterの@usernameを設定します
twitter:title | Yes | 記事のタイトルを設定します
twitter: description | Yes | 記事の説明を設定します
twitter:image | No | 画像を設定します。サイズは横幅280px以上 × 縦幅150px以上です。サイズは1MB以下でないといけません。 

他にも設定できるオプションがありますが長くなるのでここでは割愛します。
詳しくはドキュメントを参照してください。

* https://dev.twitter.com/cards/markup

### Twitterが提供しているCard Validatorで申請を行う

Twitterカードを有効にするにはTwitterが提供しているCard Validatorで申請を行う必要があります。
こちらの作業は1度行えば今後は必要ありません。

* https://cards-dev.twitter.com/validator

テキストフォームに確認したいページのURLを入力します。
エラーがなければ以下のように表示され、Twitterカードが有効になります。

![introduce_twitter_card_02](/images/introduce_twitter_card_02.png)

一度認証が成功するとそのドメインでTwitterカードの利用が有効となります(サブドメインも含む)。

## Twitterカードの種類

最後にTwitterカードの種類を紹介します。
仕様変更が行われたようで、現在は以下の4つのTwitterカードが利用できます。

* Summary Card
* Summary Card with Large Image
* App Card
* Player Card

### Summary Card

URLの概要をまとめて表示してくれるTwitterカードです。

![summary_card_example](/images/summary_card.png)

### Summary Card with Large Image

Summary Cardとほとんど同じですが、画像がより大きなサイズで表示されます。

![summary_card_with_large_image_example](/images/summary_card_with_large_image.png)

### App Card

モバイルのアプリを紹介するためのTwitterカードです。ダウンロードリンクも表示されます。

![app_card_example](/images/app_card.png)

### Player Card

ツイートに動画、オーディオを埋め込めるTwitterカードです。

![player_card_example](/images/player_card.png)

## おわりに

以上がTwitterカードの導入に関する説明となります。
Twitterカードについての詳しい情報は[公式ドキュメント](https://dev.twitter.com/ja/cards/overview)をご覧ください。
また、Twitterカードの設置に関して疑問点などあれば、ブログのコメント欄やTwitterでお気軽にご連絡ください。
