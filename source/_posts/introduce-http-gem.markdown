---
layout: post
title: "rubyでhttp通信をするhttp gem"
date: 2016-04-25 14:05:02 +0900
comments: true
categories: ruby gem
---
rubyでhttp通信をする際に便利なhttp gemというものがあるので紹介します。  
https://github.com/httprb/http

## Install


```
$ gem install http

```

## getしてみる


```ruby
$ pry
$ require ’http'
$ response = HTTP.get("https://github.com")
$ response.status
=> 200
$ response.body.to_s
=> "<html><head><meta http-equiv=\"content-type\" content=..."

```

## http メソッドを指定する
以下のように指定します。


```
>> response = HTTP.post('https://restapi.com/objects')
>> response = HTTP.put('https://restapi.com/put')
>> response = HTTP.patch('https://restapi.com/put')
>> response = HTTP.delete('https://restapi.com/put')
>> response = HTTP.head('https://restapi.com/put')

```

## クエリストリングを指定する
以下のように paramsオプションで指定します。


```
$ get = HTTP.get("http://example.com/resource", :params => {:foo => "bar"})
=> "#<HTTP::Response/1.1 404 Not …"

# 以下のように生成されたuriを確認できます
$ get.uri
=> #<HTTP::URI:0x007fce62a33aa8 URI:http://example.com/resource?foo=bar>

```

## bodyを指定する
formオプションに設定します。  
URLエンコードされたパラメーターが設定されます。


```
HTTP.post("http://example.com/resource", :form => {:foo => "42"})

```

生データを渡したいときは、bodyオプションを使います。


```
HTTP.post("http://example.com/resource", :body => "foo=42&bar=baz")

```

### ネストしたbodyの渡し方
ネストした配列を渡したい場合はjsonを使って以下のように指定できます。


```
HTTP.put("http://example.com/resource", :params => {foo: "A"}, json: { bar: { hoge: "ok!"} })

```

## Basic認証に対応する

以下のように設定ができます。


```
http = HTTP.basic_auth(user: 'user_name', pass: 'password')

```

## SSL証明書の検証を無効にする
開発環境の場合などでSSL証明書の検証を無視したい場合は以下のように設定できます。


```
ctx = OpenSSL::SSL::SSLContext.new
ctx.verify_mode = OpenSSL::SSL::VERIFY_NONE
HTTP.get("https://example.com", :ssl_context => ctx)

```
