---
layout: post
title: "base64エンコードのアルゴリズムをRubyで実装する"
date: 2016-06-13 14:08:03 +0900
comments: true
categories: ruby アルゴリズム
description: "base64エンコードのアルゴリズムを解説します。また、そのアルゴリズムをRubyで実装してみました。"
---

Base64とは英数字、記号を用いてマルチバイト文字やバイナリデータ(画像など)を扱うためのエンコード方式です。  
具体的には`A–Z, a–z, 0–9` までの62文字と、記号2つ (`+`,`/`)、さらにパディング（余った部分を詰める）のための記号として `=` が用いられます。  
7ビットの文字コードしか扱うことができない電子メールにおいてよく利用されています。

## 変換アルゴリズム

変換アルゴリズムは以下となります。

1. 元データを6ビットずつに分割する(6ビットに満たない部分は0を追加して6ビットにする)。
1. 各6ビットの値を変換表を使って4文字ずつに変換する(4文字に満たない部分は`=`記号を使って4文字にする)。

## 変換例

### 1. 元データ

文字列: "ABCDEFG"  
2進数に変換する: "0100 0001 0100 0010 0100 0011 0100 0100 0100 0101 0100 0110 0100 0111"

rubyでのサンプルコード


```ruby
"ABCDEFG".unpack("B*").pop.scan(/.{1,4}/).join(" ")

```

### 2. 6ビットずつに分割

"010000 010100 001001 000011 010001 000100 010101 000110 010001 11"


```
"ABCDEFG".unpack("B*").pop.scan(/.{1,6}/).join(" ")

```

### 3. 2ビット余るので、4ビット分0を追加して6ビットにする

"010000 010100 001001 000011 010001 000100 010101 000110 010001 110000"


```
list = "ABCDEFG".unpack("B*").pop.scan(/.{1,6}/).join(" ").split.map { |s| sprintf("%-06s", s).gsub(" ", "0")}.join(" ")

```


### 4. 変換表により、4文字ずつ変換

"QUJD", "REVG", "Rw"


```
# 変換表を作成する
keys = (0..63).map {|m| sprintf("%06s", m.to_s(2)).gsub(" ", "0")}
values = [('A'..'Z'), ('a'..'z'), ('0'..'9'), ['+', '/']].map { |a| a.to_a }.flatten
base64_table = Hash[[keys, values].transpose]

base64_list = list.map {|a| base64_table[a]}.join.scan(/.{1,4}/)
=> ["QUJD", "REVG", "Rw"]

```

### 5. 2文字余るので、2文字分 = 記号を追加して4文字にする


```
base64_list.map {|s| sprintf("%-4s", s).gsub(" ", "=")}

```

### 6. Base64文字列

"QUJDREVGRw=="


```
base64_str.scan(/.{1,4}/).map {|s| sprintf("%-4s", s).gsub(" ", "=")}.join
=> "QUJDREVGRw=="

```

## 簡易的なbase64_decodeメソッド

今までのロジックをメソッドにまとめて簡易的なbase64_decodeメソッドを作成しました。


```
class Base64
  def self.base64_encode(str)
    # 変換表を作成する
    keys = (0..63).map {|m| sprintf("%06s", m.to_s(2)).gsub(" ", "0")}
    values = [('A'..'Z'), ('a'..'z'), ('0'..'9'), ['+', '/']].map { |a| a.to_a }.flatten
    base64_table = Hash[[keys, values].transpose]

    binary = str.unpack("B*").pop.scan(/.{1,6}/).join(" ").split.map { |s| sprintf("%-06s", s).gsub(" ", "0") }
    base64_list = binary.map {|a| base64_table[a]}.join.scan(/.{1,4}/)
    base64_list.map {|s| sprintf("%-4s", s).gsub(" ", "=")}.join
  end
end

p Base64.base64_encode("ABCDEFG")
=> "QUJDREVGRw=="

```

RubyのBase64ライブラリでencodeした値と比べてみましょう。


```
require 'base64'
Base64.encode64("ABCDEFG")
=> "QUJDREVGRw==\n"

```

Rubyのencode64は最後に改行が入るようですが、encodeされた値は同じですね！

## 参考リンク

- [Base64 - Wikipedia](https://ja.wikipedia.org/wiki/Base64Base64)
