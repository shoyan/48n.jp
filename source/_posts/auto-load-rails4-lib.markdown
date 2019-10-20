---
layout: post
title: "Rails4でlibディレクトリをautoloadするときのルール"
date: 2015-10-30 13:53:03 +0900
comments: true
categories: rails
---

Rails4でlibディレクトリに作ったライブラリを読み込むときはautoloadを利用すると便利です。  
autoloadを利用するにはファイル名とクラス名がautoloadのルールに則っている必要があります。  

## autoload のパスを設定する

まず config/application.rb に autoload 用の設定を行います。

config/application.rb


```ruby
#to auto load lib/directory
config.autoload_paths += %W(#{config.root}/lib)

```

これで、lib ディレクトリ以下のファイルが、以降に説明するディレクトリ・ファイル構成と命名の規約に従うと、自動的に読み込まれるようになります。

## lib ディレクトリ以下のファイル
ファイル名は小文字、単語の区切りは _ (アンダースコア)にする。  
クラス名はアンダースコアの区切りでキャメルケースにする。

以下はファイル名とクラス名の例です。

__lib/hoge_fuga.rb__


```ruby
class HogeFuga
end

```

lib/hoge ディレクトリにファイルを置くこともできます。

__lib/hoge/fuga.rb__


```ruby
module Hoge
  class Fuga
  end
end

# 以下の様にも書けます。

class Hoge::Fuga
end

```

以下の様な書き方もできます。

__lib/hoge_moge/fuga.rb__

```ruby
module HogeMoge
  class Fuga
  end
end

# もしくは

class HogeMoge::Fuga
end

```
