---
layout: post
title: "Capistranoの導入手順とプラグインの紹介"
date: 2016-06-01 14:17:45 +0900
comments: true
categories: ruby gem
description: "Capistranoのインストールと設定、プラグインの紹介をします。Capistranoには様々なプラグインがあります。ここでは私が使っているプラグインを紹介します。"
---

Capistranoのインストールと設定、プラグインの紹介をします。

## Capistranoのインストール

bundle initコマンドでGemfileを作成します。


```
$  bundle init
Writing new Gemfile to /Users/shoyan/app/Gemfile

```

以下のGemfileが作成されます。


```
$ cat Gemfile
# A sample Gemfile
source "https://rubygems.org"

# gem "rails"

```

GemfileにCapistranoを定義します。


```
group :development do
  gem "capistrano", "~> 3.4"
end

```

bundle installコマンドでインストールします。


```
$ bundle install

```

## Capistranoの設定

Capistranoがインストールできたので、設定をしていきます。  
cap installコマンドで雛形を作成します。


```
$ bundle exec cap install

```

`config/deploy.rb`と`config/deploy/{production, staging}.rb` が作成されているので適宜編集します。

config/deploy.rb


```
# config valid only for current version of Capistrano
lock '3.5.0'

set :application, ‘app'
set :repo_url, 'git@github.com:shoyan/shoyan.git'

ask :branch, `git rev-parse --abbrev-ref HEAD`.chomp
set :deploy_to, '/var/www/app'
set :scm, :git
set :format, :airbrussh
set :keep_releases, 5

```

- **application**:  アプリケーション名
- **repo_url**: リポジトリのURI
- **branch**: デプロイ対象のブランチ
- **deploy_to**: サーバーのアプリケーションのディレクトリ
- **scm**: git or svn
- **format**: ログのフォーマット
- **keep_releases**: 過去にデプロイしたアプリケーションを何世代保持するか


デプロイするサーバーを定義します。  
事前に鍵認証でsshログインできるようにしておくととスムーズです。

config/deploy/production.rb

```
role :app, %w{shoyan@server001.example.jp shoyan@server002.example.jp}

```

## Capistranoでデプロイする

以下のコマンドでデプロイします。


```
$ bundle exec cap production deploy

```

## 便利なCapistranoプラグイン

Capistranoには様々なプラグインがあります。  
ここでは私が使っているプラグインを紹介します。

### capistrano-github-releases

デプロイ時にタグをつけたり、PRにリリースコメントをつけれます。

https://github.com/linyows/capistrano-github-releases

### capistrano/slack_notification

デプロイ時にSlackに通知できるようになります。

https://github.com/linyows/capistrano-slack_notification

### capistrano_banner

デプロイ時にASCIIアートを設定できます。  
また、productionにデプロイする前に確認をしてくれるようになります。

https://github.com/holysugar/capistrano_banner

### capistrano-bundler

デプロイ時にbundle installを実行できるようになります。

https://github.com/capistrano/bundler

### capistrano3-unicorn

デプロイ時にunicornのコマンドを実行できるようになります。

https://github.com/tablexi/capistrano3-unicorn

###capistrano-withrsync

rsyncでデプロイしてくれるプラグインです。  
デプロイするファイルが大きい場合に便利です。

https://github.com/linyows/capistrano-withrsync

