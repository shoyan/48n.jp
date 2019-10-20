---
layout: post
title: "5分でできるRubotyのインストールとプラグインチュートリアル"
date: 2016-07-29 13:55:33 +0900
comments: true
categories: ruby gem chatops
description: "RubotyはhubotクローンでRubyで書けるbotです。このチュートリアルではRubotyのインストールとプラグインの作成方法を紹介します。冗長な説明をあえて除きRubotyを動かすために重要な部分のみ解説することでスピーディにRubotyを動作できるようにしています。"
---

[Ruboty](https://github.com/r7kamura/ruboty/)はhubotクローンでRubyで書けるbotです。
このチュートリアルではRubotyのインストールとプラグインの作成方法を紹介します。
冗長な説明をあえて除きRubotyを動かすために重要な部分のみ解説することでスピーディにRubotyを動作できるようにしています。

## Rubotyをローカルで動かす

以下のコマンドでinstallします。

```
$ gem install ruboty
```

以下のコマンドでひな形を作成します。
`ruboty/` ディレクトリとその配下にGemfileが作成されます。

```
$ ruboty --generate
```

Rubotyを起動してみます。

```
$ cd ruboty
$ bundle install
$ bundle exec ruboty
```

すると対話型のプロンプトが起動します。

```
$ bundle exec ruboty
Type `exit` or `quit` to end the session.
>
```

ruboty pingコマンドを実行します。

```
> ruboty ping
pong
```

ruby helpコマンドで一覧が見れます。

```
> ruboty help
ruboty /help( me)?(?: (?<filter>.+))?\z/i - Show this help message
ruboty /ping\z/i - Return PONG to PING
ruboty /who am i\?/i - Answer who you are
```

## Rubotyプラグインを作成する

Ruboty はhubotと同様にプラグインで拡張できます。

Helloプラグインを作成してみましょう。
Helloプラグインはhelloと挨拶すると、helloと挨拶を返すだけのプラグインです。

hello.rb

```ruby
module Ruboty
  module Handlers
    class Hello < Base
      on(/hello/i, name: “hello”, description: "こんにちは")

      def hello(message)
        message.reply("Hello!!")
      end
    end
  end
end
```

`Ruby::handlers`の名前空間の下にプラグインの名前でクラスを作成し、`on` メソッドを定義します。
`on` メソッドの第1引数はコマンドです。正規表現で定義できます。
第2引数は呼び出すメソッド名、コマンドの説明等のオプションを指定します。

実行してみましょう。
`-l` オプションで読み込むファイルを指定することができます。

```
⇒  bundle exec ruboty -l hello.rb
Type `exit` or `quit` to end the session.
> ruboty hello
Hello!!
```

また、bot名のprefixなしに実行することもできます。
allオプションを使って実装します。

サンプルとして、ぬるぽプラグインを実装します。
これはぬるぽという言葉に反応するプラグインです。

nullpo.rb

```ruby
module Ruboty
  module Handlers
    class NullPoHandler < Base
      on(/.*(ぬるぽ|ヌルポ).*/, name: 'nullpo', description:'ぬるぽに反応します', all: true)

      def nullpo(message)
        message.reply("ガッ!!!!")
      end
    end
  end
end
```

実行してみましょう。
`-l` オプションで読み込むファイルを指定することができます。

```
$ bundle exec ruboty -l nullpo.rb
Type `exit` or `quit` to end the session.
> ほげ ぬるぽ ほげ
ガッ!!!!
```

bot名のprefixがなくても反応していることが確認できます。

次回はSlackと連携させる方法を紹介します。

* [Botsインテグレーションを使って手軽にRubotyをSlackで動かす](/blog/2016/08/02/ruboty-and-slack/)
