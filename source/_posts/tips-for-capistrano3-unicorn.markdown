---
layout: post
title: "capistrano3-unicornを使う"
date: 2016-04-19 17:21:17 +0900
comments: true
categories: ruby
---

capistrano3-unicornとは、capistranoでデプロイしたときにunicornのstart/restartをしてくれるgemです。
https://github.com/tablexi/capistrano3-unicorn

### Install
`Gemfile`に以下を追記して`bundle install`します。


```ruby
group :development do
  gem 'capistrano3-unicorn'
end

```

`Capfile`に以下を追記します。


```ruby
require 'capistrano3/unicorn'

```

`config/deploy.rb`に以下を追記します。


```ruby
after 'deploy:publishing', 'deploy:restart'
namespace :deploy do
  task :restart do
    invoke 'unicorn:restart'
  end
end

```

これで設定完了です。
cap deployすればunicorn restartが実行されるようになります。

### 注意点ポイント

設定していて少しわかり辛かった点があります。

unicorn.rbがデフォルトでは`CURRENT_PATH/config/unicorn/RAILS_ENV.rb`に設定されています。  
sinatraなどのrails以外のアプリケーションの場合は、RAILS_ENVがセットされていないため、`CURRENT_PATH/config/unicorn/.rb`のようになってしまいます。
ですので、明示的に設定が必要です。  
しかし、設定方法についてドキュメントに明記されてないので少し戸惑いました。

設定はソースを参考にしました。

https://github.com/tablexi/capistrano3-unicorn/blob/master/lib/capistrano3/tasks/unicorn.rake#L4

#### unicorn_config_pathの設定


以下のようにlambdaを使って設定します。  
config/unicorn.rbをunicorn_config_pathとして設定しています。  
config/deploy.rb


```ruby
set :unicorn_config_path, -> { File.join(current_path, "config", "unicorn.rb") }

```
