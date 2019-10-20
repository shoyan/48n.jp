---
layout: post
title: "Guardでrspecのテストを自動化する"
date: 2016-05-24 15:05:01 +0900
comments: true
categories: ruby gem 自動化
description: "Guardはファイルを監視して、ファイルになんらかの変更がされたら、指定した処理を実行するツールです。この機能を使ってファイルが変更されたらテストを自動で実行させたり、シンタックスチェックをすることができます。今回はrspecで自動的にテストを実行する方法を紹介します。"
---

自動化ツールのGuardの紹介をします。  
Guardはファイルを監視して、ファイルになんらかの変更がされたら、指定した処理を実行するツールです。  
この機能を使ってファイルが変更されたらテストを自動で実行させたり、シンタックスチェックをすることができます。  
今回はrspecで自動的にテストを実行する方法を紹介します。  

まずはGuardのinstallをします。  
Gemfileに定義してインストールします。  

Gemfile


```ruby
group :development do
  gem 'guard'
  gem 'guard-rspec', require: false
end

```

bundle installして、Guardfileを作成します。  
GuardfileはGuardの設定を定義するファイルです。


```
$ bundle install
$ bundle exec guard init rspec

```

guard init rspecを実行するとrspecの設定が書かれたGuardfileが作成されます。  
Railsを想定した設定が書かれていますので、Railsの場合はそのままでOKです。

## ファイルが実行されたらrspecを実行する

ファイルが実行されたらrspecを実行するようにしましょう。  
別でウィンドウを開いてguardを実行します。  


```
$ bundle exec guard

```

ファイルを変更するとそのファイルのテストが実行され、テスト結果が表示されます。

## Guard-rspecのDSL

Guardの設定はGuardのDSLを用いて設定します。


```
# rspecのグループを定義し、監視しているファイルに変更があった場合は"bundle exec rspec""を実行する。
guard :rspec, cmd: "bundle exec rspec" do
  # Guard::RspecのDSLのインスタンスを作成
  dsl = Guard::RSpec::Dsl.new(self)
  rspec = dsl.rspec
  watch(rspec.spec_helper) { rspec.spec_dir }
end

```

watchで監視するファイルを設定します。  
watchの引数は以下です。  


```text
watch(監視するファイル) { コマンドに渡される引数 }

```

ここででてきた、`rspec.spec_helper` と `rspec.spec_dir` はどんな値を返すのでしょうか。
pryで覗いてみましょう。


```ruby
$ pry
> require 'guard/rspec/dsl'
> dsl = Guard::RSpec::Dsl.new(self)
> rspec = dsl.rspec
> rspec.spec_helper
=> "spec/spec_helper.rb"
> rspec.spec_dir
=> "spec"

```

ソースを少し見てみましょう。


```
def rspec
  @rspec ||= OpenStruct.new(to_s: "spec").tap do |rspec|
    rspec.spec_dir = "spec"
    rspec.spec = -(m) { Dsl.detect_spec_file_for(rspec, m) }
    rspec.spec_helper = "#{rspec.spec_dir}/spec_helper.rb"
    rspec.spec_files = %r{^#{rspec.spec_dir}/.+_spec\.rb$}
    rspec.spec_support = %r{^#{rspec.spec_dir}/support/(.+)\.rb$}
  end
end

```

https://github.com/guard/guard-rspec/blob/master/lib/guard/rspec/dsl.rb#L28

実装には`OpenStruct`が使われています。  
`OpenStruct`とは要素を動的に追加・削除できる手軽な構造体を提供するクラスです  
http://docs.ruby-lang.org/ja/2.1.0/class/OpenStruct.html

要素を追加するためにtapメソッドを使っています。  
`OpenStruct`とtapメソッドをうまく使っていますね。  

実は、というか当たり前なのですがOpenStructなので値の上書きも簡単にできてしまいます。


```
> rspec.spec_helper
=> "spec/spec_helper.rb"
> rspec.spec_helper = 'hoge'
=> "hoge"
> rspec.spec_helper
=> "hoge"

```

## Guardのカスタマイズ

Guardの様々なプラグインが開発されています。  
プラグインは以下のページで参照できます。  
https://github.com/guard/guard/wiki/Guard-Plugins

ちなみに、今回使った`guard-rspec`もGuardのプラグインです。  
他にも様々なプラグインが用意されています。

## 関連記事

* [Guardでrubocopを自動化する](/blog/2016/12/07/guard-rubocop/)
