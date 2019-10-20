---
layout: post
title: "SinatraのインストールとRspecでテストする"
date: 2016-04-20 13:53:28 +0900
comments: true
categories: ruby sinatra
---

RubyといえばRuby on Railsが有名ですが、DBを使わないシンプルなアプリケーションの場合はSinatraで十分な気がします。  
この記事では、SinatraのインストールとRspecでテストする方法を紹介します。

### SinatraのInstall
sinatra_sampleというディレクトリを作成してそこにアプリを作成します。


```
mkdir sinatra_sample
cd sinatra_sample

```

Gemfileを作成します。


```
# Gemfile
source 'https://rubygems.org'

gem 'rake'
gem 'sinatra'

```

`bundle install` でインストールします。

### アプリケーションを作成

myapp.rbを作成します。


```ruby
# my_app.rb
require 'sinatra'

get '/' do
  'Hello world!'
end

```

以下のコマンドで実行します。


```
ruby myapp.rb

```

ブラウザで以下にアクセスするとHello world!と表示されます。  
http://localhost:4567

あっという間にできましたね。

### Rspecでテストをする

RspecでテストするためにRspecをインストールします。


```
# Gemfile

source 'https://rubygems.org'

gem 'rake'
gem 'sinatra'

group :test do
  gem 'rspec'
  gem 'rack-test'
end

```

`bundle install`でインストールします。

spec/spec_helper.rb を作成します。  
spec_helper.rbはrspecの設定を行うためのファイルです。


```ruby
# spec/spec_helper.rb
require 'rack/test'
require 'rspec'

ENV['RACK_ENV'] = 'test'

require File.expand_path '../../myapp.rb', __FILE__

module RSpecMixin
  include Rack::Test::Methods
  def app() Sinatra::Application end
end

RSpec.configure { |c| c.include RSpecMixin }

```

spec/myapp_spec.rbを作成します。  
myapp_spec.rbはアプリケーションをテストするためのファイルです。


```ruby
# spec/app_spec.rb
require File.expand_path '../spec_helper.rb', __FILE__

describe "My Sinatra Application" do
  it "should allow accessing the home page" do
    get '/'
    expect(last_response).to be_ok
  end
end

```

テストを実行してみましょう。


```
bundle exec rspec spec

```


```
⇒  bundle exec rspec spec
.

Finished in 0.03119 seconds (files took 0.20975 seconds to load)
1 example, 0 failures

```

テストが成功しました！


### 参考文献

資料は以下を参照しました。

* [英語]http://www.sinatrarb.com/intro.html
* [日本語]http://www.sinatrarb.com/intro-ja.html
* [Sinatra Recipes]http://recipes.sinatrarb.com/
