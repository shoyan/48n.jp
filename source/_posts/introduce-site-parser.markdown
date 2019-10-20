---
layout: post
title: "Webサイトのメタタグを取得するsite-parserをつくった"
date: 2016-04-27 13:37:10 +0900
comments: true
categories: ruby
---

Webサイトのメタデータを抽出するツールを作成しました。  
https://github.com/shoyan/site-parser

## 使い方

1. git clone git@github.com:shoyan/site-parser.git
1. cd site-parser
1. bundle install
1. site.csv をテキストエディタで編集
1. ruby site-parser.rb

site.csv にパースしたいURLを記入して `ruby site-parse.rb` とすればメタデータが表示されます。


```ruby
$ ruby site-parser.rb
"http://www.yahoo.co.jp/"
[
    [0] {
                             "url" => "http://www.yahoo.co.jp/",
                           "title" => "Yahoo! JAPAN",
                     "description" => "日本最大級のポータルサイト。検索、オークション、ニュース、メール、コミュニティ、ショッピング、など80以上のサービスを展開。あなたの生活をより豊かにする「ライフ・エンジ ン」を目指していきます。",
                          "robots" => "noodp",
        "google-site-verification" => "fsLMOiigp5fIpCDMEVodQnQC7jIY1K3UXW5QkQcBmVs"
    }
]

```

また、サーバーを起動してAPIとして利用することもできます。

### サーバーを起動


```
$ ruby server.rb
== Sinatra (v1.4.7) has taken the stage on 4567 for development with backup from Thin
Thin web server (v1.6.4 codename Gob Bluth)
Maximum connections set to 1024
Listening on 0.0.0.0:4567, CTRL+C to stop

```

### APIを実行


```
$ curl http://localhost:4567 -X POST -d "url=http://www.yahoo.co.jp/"
=> {"url":"http://www.yahoo.co.jp/","title":"Yahoo! JAPAN","description":"日本最大級のポータルサイト。検索、オークション、ニュース、メール、コミュニティ、ショッピング、など80以上のサービスを展開。あなたの生活をより豊かにする「ライフ・エンジン」を目指していきます。","robots":"noodp","google-site-verification":"fsLMOiigp5fIpCDMEVodQnQC7jIY1K3UXW5QkQcBmVs"}

```

## その他

内部的にはNokogiriを使ってhtmlをパースしています。

文字化けして内容がみれない場合があったので、以下のワークアラウンドをいれました。


```ruby
html = URI.parse(url).read
node = Nokogiri::HTML(html.toutf8, nil, 'utf-8')

```

## 参考資料

以下が参考になりました。ありがとうございます。

- Nokogiri を使った Rubyスクレイピング [初心者向けチュートリアル]
  - http://morizyun.github.io/blog/ruby-nokogiri-scraping-tutorial/
- kogiriで文字化けを防ぐ
  - http://qiita.com/foloinfo/items/435f0409a6e33929ef3c
- [Ruby]スクレイピングのためのNokogiri利用メモ
  - http://d.hatena.ne.jp/otn/20090509/p1
