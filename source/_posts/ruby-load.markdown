---
layout: post
title: "Rubyのロード機構について"
date: 2016-07-19 14:07:16 +0900
comments: true
categories: ruby
description: "Rubyはロード機構として、require, load, autoload, require_relativeを備えています。それぞれのメソッドの特徴と使いかたを紹介します。"
---

Rubyはロード機構として、以下を備えています。

- require
- load
- autoload
- require_relative

## require

requireは以下の特徴を備えています。

- ロードパスからファイルを探してくる
- 拡張ライブラリもロードできる
- 拡張子 .rb / .so を省略できる
- 同じファイルは2度以上ロードしない

Rubyのロードパスは `$:`に入っています。
私の環境では以下のように表示されました。

```
[1] pry(main)> puts $:
/Users/shoyan/.rbenv/rbenv.d/exec/gem-rehash
/Users/shoyan/.rbenv/versions/2.3.0/lib/ruby/gems/2.3.0/gems/did_you_mean-1.0.0/lib
/Users/shoyan/.rbenv/versions/2.3.0/lib/ruby/gems/2.3.0/gems/coderay-1.1.1/lib
/Users/shoyan/.rbenv/versions/2.3.0/lib/ruby/gems/2.3.0/gems/slop-3.6.0/lib
/Users/shoyan/.rbenv/versions/2.3.0/lib/ruby/gems/2.3.0/gems/method_source-0.8.2/lib
/Users/shoyan/.rbenv/versions/2.3.0/lib/ruby/gems/2.3.0/gems/pry-0.10.3/lib
/Users/shoyan/.rbenv/versions/2.3.0/lib/ruby/site_ruby/2.3.0
/Users/shoyan/.rbenv/versions/2.3.0/lib/ruby/site_ruby/2.3.0/x86_64-darwin14
/Users/shoyan/.rbenv/versions/2.3.0/lib/ruby/site_ruby
/Users/shoyan/.rbenv/versions/2.3.0/lib/ruby/vendor_ruby/2.3.0
/Users/shoyan/.rbenv/versions/2.3.0/lib/ruby/vendor_ruby/2.3.0/x86_64-darwin14
/Users/shoyan/.rbenv/versions/2.3.0/lib/ruby/vendor_ruby
/Users/shoyan/.rbenv/versions/2.3.0/lib/ruby/2.3.0
/Users/shoyan/.rbenv/versions/2.3.0/lib/ruby/2.3.0/x86_64-darwin14
```

## load

loadもrequireと同じくロードパスからファイルを探してロードします。
しかし、requireのように拡張ライブラリはロードできません。
また、ロードできるファイルもRubyプログラムだけです。
requireのように2回以上の読み込みを制御する機構もなく、callされた回数分ファイルは読み込まれます。

## autoload

autoloadはrequireと似ていますが、すぐには読み込まれず、必要になった段階でrequrieされます。以下、サンプルコードです。

autoload.rb

```ruby
autoload :Outer, './outer'
puts Outer.new
```

outer.rb

```
class Outer
end

puts 'Outer loaded!'
```

実行すると、Outerがloadされているのが確認できます。

```
⇒  ruby autoload.rb
Outer loaded!
#<Outer:0x007fcc14040e60>
```

autoloadはネストされたクラスやモジュールもloadすることができます。

autoload.rb

```
autoload :Outer, './outer'
puts Outer.new
puts Outer::Inner.new
```

outer.rb

```
class Outer
  autoload :Inner, './inner'
end

puts 'Outer loaded!'
```

inner.rb

```
class Outer
  class Inner
  end
end

puts "Outer::Inner loaded!"
```

実行すると、Outer::Innerがloadされているのが確認できます。

```
⇒  ruby autoload.rb
Outer loaded!
#<Outer:0x007f9fd4054df0>
Outer::Inner loaded!
#<Outer::Inner:0x007f9fd404f990>
```

## require_relative

あまり見かけることがありませんが、`require_relative` というメソッドがRuby1.9.2から追加されています。
例えば`require`で同じ階層のファイルをロードする場合は、`require './filename'` のように明示的に`./`を指定する必要があります。
`require_relative`を使えば、`require_relative 'filename'` のように定義することができます。
