---
layout: post
title: "Guardでrubocopを自動化する"
date: 2016-12-07 13:55:09 +0900
comments: true
categories: 自動化 ruby
description: "RubyのシンタックスチェックにRubocopを使っていて、リポジトリにpushした時にチェックするようにしている。push→シンタックスエラー→直してもう1回pushというのがまどろこしいのでGuardを使ってファイルを保存する度にチェックするようにしてみた。"
---

RubyのシンタックスチェックにRubocopを使っていて、リポジトリにpushした時にチェックするようにしている。
push→シンタックスエラー→直してもう1回pushというのがまどろこしいので[guard-rubocop](https://github.com/yujinakayama/guard-rubocop)を使ってファイルを保存する度にチェックするようにしてみた。
ストレスが軽減され、良い感じだったので紹介する。

## guard-rubocopの導入

gurad-rubocopを導入するにはまず、rubocopを導入している必要がある。
この記事ではrubocopは導入済みという前提で進める。

gurad-rubocopの導入はいたって簡単。
3分で導入できるので是非やってほしい。

Gemfileに以下を定義する。

```ruby
group :development do
  gem 'guard'
  gem 'guard-rubocop', require: false
end
```

`bundle install` を行うと、guardコマンドが利用できるようになる。
`bundle exec guard init rubocop` で `Guardfile` が生成される。

これでインストール完了。

あとはターミナルで `bundle exec guard` コマンドを実行する。
その状態でファイルを変更するとそのファイルを対象にrubocopコマンドが実行される。

## オプション

デフォルトだとgurad起動時にrubocopコマンドが実行されるようになっている。
起動時に実行したくない時はGuardfileに以下の設定を行えばよい。

```
guard :rubocop do

```

以下のように変更する。


```
guard :rubocop, all_on_start: false do

```

他にもオプションがあるので詳しくはREADMEを参照してほしい。

* https://github.com/yujinakayama/guard-rubocop#options

### 関連記事

* [Guardでrspecのテストを自動化する](/blog/2016/05/24/introduce-guard-gem-and-guard-rspec/)
