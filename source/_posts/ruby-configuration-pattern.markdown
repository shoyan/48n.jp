---
layout: post
title: "[Ruby]オブジェクトの設定情報を保持するパターンの紹介"
date: 2016-07-20 18:42:04 +0900
comments: true
categories: ruby
description: "オブジェクトの設定情報を保持するパターンを紹介します。このパターンを使えば設定情報を管理するクラスに集約することができ、コードの見通しがよくなります。また、初期設定の定義ができるようになります。"
---

オブジェクトの設定情報を保持するパターンを紹介します。
このパターンを使えば設定情報を管理するクラスに集約することができ、コードの見通しがよくなります。また、初期設定の定義ができるようになります。

名前空間で区切ってConfigurationモデルに設定を持たせるようにします。
そして、トップにconfigurationとconfigureメソッドを定義します。
クラスメソッドで定義するのがポイントです。

```ruby
class Hoge
  class Configuration
    def initialize
      @name = nil
    end
    attr_accessor :name

  end

  def self.configuration
    @configuration ||= Hoge::Configuration.new
  end

  def self.configure
    yield configuration if block_given?
  end
end
```

`configure`メソッドは以下のように初期設定したいパラメーターを設定するために使います。

```
Hope.configure do |config|
  config.name = 'shoyan'
end

config = Hoge.configuration
config.name
=> "shoyan"
```

`configure`メソッドで設定した情報が保持されています。
初期設定はできるようになりました。

次はこの設定情報を内部で参照するようにします。

Hopeクラスのinitializeメソッドで設定情報をインスタンス変数に保存します。

```
class Hoge
  def initialize
    @config = Hoge.configuration
  end

  def greeting
    puts "Hello #{@config.name}"
  end
end
```
実行してみます。
Hope::Configurationの値が出力されていますね。

```
hoge = Hoge.new
hope.greeting
=> Hello shoyan
```


Hoge.rb

```
class Hoge
  class Configuration
    def initialize
      @name = nil
    end
    attr_accessor :name
  end

  def initialize
    @config = Hoge.configuration
  end

  def greeting
    puts "Hello #{@config.name}"
  end

  def self.configuration
    @configuration ||= Hoge::Configuration.new
  end

  def self.configure
    yield configuration if block_given?
  end
end
```
