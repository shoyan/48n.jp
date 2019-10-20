---
layout: post
title: "Sinatraのレスポンスの設定とストリーミングヘルパー"
date: 2016-04-26 13:52:30 +0900
comments: true
categories: sinatra ruby
---

通常はルーティングブロックの戻り値にセットした文字列がbodyにセットされます。  
任意の評価フローでbodyをセットしたい場合はどうすればいいでしょうか。  
Sinatraには任意の評価フローでbodyをセットできる、bodyヘルパーが用意されています。  


```ruby
get '/foo' do
  body "bar"
end

after do
  puts body
end

```

Status Codeを設定するstatusヘルパー、headerを設定するheadersヘルパーも用意されています。


```
get '/foo' do
  status 418
  headers \
    "Allow"   => "BREW, POST, GET, PROPFIND, WHEN",
    "Refresh" => "Refresh: 20; http://www.ietf.org/rfc/rfc2324.txt"
  body "I'm a tea pot!"
end

```

引数の伴わないbody, status, headersは現在の値を確認するために使えます。

### Streamingヘルパー

streamヘルパーを使うとおもしろいことができます。  
レスポンスボディの部分を生成している段階でデータを送信することができます。  
この仕組みを使ってストリーミングAPIを実装することもできます。  


```
get '/' do
  stream do |out|
    out << "It's gonna be legen -\n"
    sleep 0.5
    out << " (wait for it) \n"
    sleep 1
    out << "- dary!\n"
  end
end

```

以下は、ストリーミングAPIのサンプルです。


```
# app.rb
# long polling

set :server, :thin
connections = []

get '/subscribe' do
  # register a client's interest in server events
  stream(:keep_open) do |out|
    connections << out
    # purge dead connections
    connections.reject!(&:closed?)
  end
end

post '/message' do
  connections.each do |out|
    # notify client that a new message has arrived
    out << params['message'] << "\n"

    # indicate client to connect again
    out.close
  end

  # acknowledge
  "message received"
end

```

ストリーミングはThinやRainbowsなどのイベント型サーバーでないと動かないので注意が必要です。  

サーバーを起動させてみます。


```
$ gem insatall thin
$ ruby app.rb

```

別のターミナルを開いて、以下を実行します。


```
$ curl http://localhost:4567/subscribe

```

何もレスポンスがありませんが、接続が維持された状態となります。

別のタブを開いて以下の実行してみましょう。


```
$ curl http://localhost:4567/message -X POST -d "message=hello"

```

すると、subscribeのほうにhelloと表示されて接続が切れます。  
これは、/messageのout.closeを実行しているからです。  
試しに、out.closeをコメントアウトしてサーバーを起動すると接続維持されたままになります。
