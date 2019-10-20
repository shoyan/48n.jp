---
layout: post
title: "Rubyでファイルエンコーディングを確認する"
date: 2016-06-16 13:46:56 +0900
comments: true
categories: ruby
description: "Rubyでファイルエンコーディングを確認するにはNKFモジュールを使って確認します。またrspecでの利用方法を紹介します。"
---

Rubyでファイルエンコーディングを確認するにはNKFモジュールを使って確認します。

http://docs.ruby-lang.org/ja/2.3.0/class/NKF.html

NKFモジュールとは、nkf(Network Kanji code conversion Filter, http://sourceforge.jp/projects/nkf/) をRubyから使うためのモジュールです。


```ruby
require 'nkf'

content = File.read("path/to/file.txt")
NKF.guess(content).to_s
=> "Shift_JIS"

```

このような感じでrspecでテストするときにも使えます。


```
require 'nkf'

it 'file encoding is Shift_JIS' do
 content = File.read("filename.csv")
 expect(NKF.guess(content).to_s).to eq 'Shift_JIS'
end

```
