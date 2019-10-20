---
layout: post
title: "RACK_ENVとUnicorn、SinatraでのRACK_ENVの扱いと注意点"
date: 2016-05-02 14:14:29 +0900
comments: true
categories: ruby sinatra rack
description: "RACK_ENVについて調べた内容とUnicorn、SinatraでのRACK_ENVの扱い方や注意点をまとめました。"
---

RACK_ENVについて調べた内容とUnicorn、SinatraでのRACK_ENVの扱い方や注意点をまとめました。  
RACK_ENVとはRACKの環境変数です。  
何を設定するかによって使用するmiddlewareが変わります。  
RACK_ENVには、`development`、`deployment`、`none`の3種類があります。

以下がそれぞれのRACK_ENVで使われるmiddlewareです。


```
    #       - development: CommonLogger, ShowExceptions, and Lint
    #       - deployment: CommonLogger
    #       - none: なし

```
https://github.com/rack/rack/blob/028438ffffd95ce1f6197d38c04fa5ea6a034a85/lib/rack/server.rb#L157

`development`と`deployment`に該当しない場合はmiddlewareは何も使われないようです。

https://github.com/rack/rack/blob/028438ffffd95ce1f6197d38c04fa5ea6a034a85/lib/rack/server.rb#L228

ここを間違えて`production`と設定する例を時々見るのですが、`production`は存在しないので、`none`と同じ挙動となります。
capistrano3-unicornのソースコードを見てみると、`production`を指定した場合`deployment`を設定するようになっていました。


```ruby
set :unicorn_rack_env, -> { fetch(:rails_env) == "development" ? "development" : "deployment" }

```
https://github.com/tablexi/capistrano3-unicorn/blob/master/lib/capistrano3/tasks/unicorn.rake#L7

unicornはちょっと挙動を変えていて、`development`と`deployment`のときは ShowExceptionsとLintは読み込まないようです。


```
      case ENV["RACK_ENV"]
      when "development"
      when "deployment"
        middleware.delete(:ShowExceptions)
        middleware.delete(:Lint)
      else
        return inner_app
      end

```
https://github.com/defunkt/unicorn/blob/master/lib/unicorn.rb#L79

### Sinatraの環境変数

SinatraはRACK_ENVとenvironmentという環境変数が2つありややこしいです。  
基本的には、environmentを定義して使います。  
異なる環境で実行したい場合、RACK_ENVを指定することができます(これが混乱のもと)。


```text
異なる環境を走らせるには、RACK_ENV環境変数を設定します。
RACK_ENV=production ruby my_app.rb

```
https://github.com/sinatra/sinatra/blob/4c7d38eb1b2cc02ce51029f28e0c3c34ca12ccfd/README.ja.md#%E7%92%B0%E5%A2%83%E8%A8%AD%E5%AE%9Aenvironments

`set :environment, :production` と定義して、`RACK_ENV=development ruby my_app.rb` を実行してみました。  
このときの `settings.production?`の戻り値は何になるでしょうか。  
trueとなり、productionと判定されます。  
set :environmentで指定されたもので判定されるようです。

まとめると以下のような挙動になります。

* set :environment が指定されているときは指定された値が使われる
* set :environment が指定されていないときは RACK_ENVが使われる
* set :environment も RACK_ENVも指定されていないときは developmentになる

ここで1つ気になることがでてきました。  
capistrano3-unicornを使った場合は、RACK_ENVはdeploymentが指定されます。  
Sinatraで `set :environment` を設定しなかった場合はRACK_ENVの値が優先されます。  
ということは、本番環境でも`settings.production?`がfalseとなってしまいます。  
これは意図しない挙動ですね。  
また、テンプレートのキャッシュについても効かなくなってしまいます。

### まとめ
Sinatraではset :environment を明示的に指定しましょう。


```ruby
set :environment, ENV["RACK_ENV"] == "deployment"? :production : ENV["RACK_ENV"].to_sym

```

## 参考文献
* https://blog.kksg.net/posts/unicorn-environment
* http://unicorn.bogomips.org/unicorn_1.html#TOC
* https://github.com/sinatra/sinatra/blob/4c7d38eb1b2cc02ce51029f28e0c3c34ca12ccfd/README.ja.md#%E7%92%B0%E5%A2%83%E8%A8%AD%E5%AE%9Aenvironments
