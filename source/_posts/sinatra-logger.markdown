---
layout: post
title: "Sinatraのロギング機構について調べてみた"
date: 2016-05-27 13:48:34 +0900
comments: true
categories: sinatra rack
description: "Sinatraのloggerヘルパーを使ったところ、なぜか標準エラーの出力先にログが吐かれており、標準出力の出力先にはログが吐かれない。
標準出力先にログを吐くものだと思っていたのだが、自分が想定していた挙動と違うので調べてみた。Sinatraのロギング機構の仕組みと任意のファイルにログを出力する方法を解説する。"
---

Sinatraのloggerヘルパーを使ったところ、なぜか標準エラーの出力先にログが吐かれており、標準出力の出力先にはログが吐かれない。  
標準出力先にログを吐くものだと思っていたのだが、自分が想定していた挙動と違うので調べてみた。

まずは、Sinatraのloggerヘルパーのソースコードを確認してみる。


```ruby
def logger
  request.logger
end

```

https://github.com/sinatra/sinatra/blob/939ce04c1b77d24dd78285ba0836768ad57aff6c/lib/sinatra/base.rb#L327

request.loggerを返している。  
レシーバであるrequestは rack::requestなので、rack::request#loggerは何を返しているかを確認する。


```
def logger; get_header(RACK_LOGGER) end

```

https://github.com/rack/rack/blob/master/lib/rack/request.rb#L136

get_headerは@envから引数に与えられた値を返すだけのメソッド。


```
def get_header(name)
  @env[name]
end

```

RACK_LOGGERは以下のように定義されている。


```
RACK_LOGGER = 'rack.logger'.freeze

```

https://github.com/rack/rack/blob/9073125f71afd615091f575d74ec468a0b1b79bf/lib/rack.rb#L64

ここまでで、loggerヘルパーは`env['rack.logger’]`を取得していることがわかった。

では、rack.loggerには何が設定されているのかという疑問が湧いてくる。  
rackには3つのロガーがある。

- CommonLogger
- Logger
- NullLogger

このうち、LoggerとNullLoggerがRACK_LOGGERにセットしていた。


```
# Rack::Logger
require 'logger'

module Rack
  # Sets up rack.logger to write to rack.errors stream
  class Logger
    def initialize(app, level = ::Logger::INFO)
      @app, @level = app, level
    end

    def call(env)
      logger = ::Logger.new(env[RACK_ERRORS])
      logger.level = @level

      env[RACK_LOGGER] = logger
      @app.call(env)
    end
  end
end

# Rack::NullLogger
module Rack
  class NullLogger
    def initialize(app)
      @app = app
    end

    def call(env)
      env[RACK_LOGGER] = self
      @app.call(env)
    end
    ...........

```

通常、Rack::Loggerが使われる。  
Rack::LoggerはRubyのloggerライブラリのラッパーで、log deviceにenv[RACK_ERRORS]をセットしている。  
env[RACK_ERRORS]が何かを調べたところ、基本的には$stderrがセットされるようだ。

(例)webrickの場合は、$stderrがセットされている。


```
env.update(
  RACK_VERSION => Rack::VERSION,
  RACK_INPUT => rack_input,
  RACK_ERRORS => $stderr,
  RACK_MULTITHREAD => true,
  RACK_MULTIPROCESS => false,
  RACK_RUNONCE => false,
  RACK_URL_SCHEME => ["yes", "on", "1"].include?(env[HTTPS]) ? "https" : "http",
  RACK_IS_HIJACK => true,
  RACK_HIJACK => lambda { raise NotImplementedError, "only partial hijack is supported."},
  RACK_HIJACK_IO => nil
)

```

https://github.com/rack/rack/blob/95172a60fe5c2a3850163fc75e0981fe440c064e/lib/rack/handler/webrick.rb#L68

ということで、結果的にSinatraのloggerは標準エラーに出力されることになる。

## アプリケーションログを任意のファイルに出力するには

任意のファイルにログを出力したい場合は、自前でloggerを定義してやればよい。


```
def logger
  return @logger unless @logger.nil?
  file = File.new("#{settings.root}/log/#{settings.environment}.log", 'a+')
  file.sync = true
  @logger = ::Logger.new(file)
end

logger.info "Hello"

```

## 参考リンク

- [Sinatra でアプリケーションログをファイルに書く方法。](http://koseki.hatenablog.com/entry/20120309/SinatraAppLog)
- [Sinatra Recipes - Middleware - Rack Commonlogger](http://recipes.sinatrarb.com/p/middleware/rack_commonlogger)
