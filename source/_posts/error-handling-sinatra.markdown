---
layout: post
title: "Sinatraのエラーハンドリング"
date: 2016-05-12 13:29:14 +0900
comments: true
categories: sinatra ruby
description: "Sinatraのエラーハンドリングにはnot_foundハンドラとerrorハンドラの2つのハンドラが用意されています。この2つのerrorハンドラの使い方を説明します。"
---

Sinatraには not_foundハンドラとerrorハンドラの2つのハンドラが用意されています。  
not_foundハンドラは404エラーを補足するためのエラーハンドラです。  
errorハンドラは様々なエラーを補足するためのエラーハンドラです。  

## Not Foundハンドラ

not_foundハンドラは404エラーを補足するためのエラーハンドラです。  
`Sinatra::NotFound`が発生したとき、またはステータスコードが404のときは not_foundハンドラが実行されます。


```ruby
not_found do
  'This is nowhere to be found.'
end

```

## Errorハンドラ

errorハンドラは様々なエラーを補足するためのエラーハンドラです。  
例外オブジェクトにはRack変数の sinatra.error でアクセスできます。


```
error do
  'Sorry there was a nasty error - ' + env['sinatra.error'].message
end

```

以下の設定をすると、environmentがDevelopmentのときにブラウザにスタックトレースを表示することができます。


```
set :show_exceptions, :after_handler

```

エラー固有の制御もできます。
MyCustomeErrorのエラーハンドリングをしたいときは以下のように定義します。


```
error MyCustomError do
  'So what happened was...' + env['sinatra.error'].message
end

```

raiseでエラーを発生させるようにしてみます。


```
get '/' do
  raise MyCustomError, 'something bad'
end

# 以下のようにレスポンスが返ります。

So what happened was... something bad

```

ステータスコードを指定してエラーハンドリングを行う方法もあります。


```
error 403 do
  'Access forbidden'
end

```

レンジの指定も可能です。


```
error 400..510 do
  'Boom'
end

```

### errorハンドラにerrorコードを指定しなかった場合は何を補足するのか
errorハンドラにerrorコードを指定しなかった場合は、Exceptionを補足します。


```
      def error(*codes, &block)
        args  = compile! "ERROR", /.*/, block
        codes = codes.map { |c| Array(c) }.flatten
        codes << Exception if codes.empty? #errorコードの指定がない場合
        codes << Sinatra::NotFound if codes.include?(404)
        codes.each { |c| (@errors[c] ||= []) << args }
      end

```

https://github.com/sinatra/sinatra/blob/939ce04c1b77d24dd78285ba0836768ad57aff6c/lib/sinatra/base.rb#L1287

その他の例外は補足しません。  
例えばExceptionのサブクラスであるStandardErrorは拾ってくれません。  
ですので、明示的にerrorコードを指定しておいたほうがよいです。


```
error 500 do
  'Sorry there was a nasty error - ' + env['sinatra.error'].message
end

```

## 参考文献

- http://www.sinatrarb.com/intro.html#Error%20Handling
