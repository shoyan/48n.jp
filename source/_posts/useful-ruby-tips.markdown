---
layout: post
title: "知っていると便利なRubyの小技集"
date: 2016-07-14 13:43:40 +0900
comments: true
categories: ruby
description: "知っていると便利なRubyの小技集を紹介します。"
---

### a-zの文字列を作成する

```ruby
('a'..'z').to_a.join
=> "abcdefghijklmnopqrstuvwxyz"
```

### 特定のkeyのみ抽出する

特定のkeyのみ抽出するにはvalues_atメソッドを使います。

```
h = { "cat" = "feline", "dog" = "canine", "cow" = "bovine" }
h.values_at("cow", "cat")  #=> ["bovine", "feline"]
```

### keyとvalueの配列からhashを作成する

```
keys   = ["suzuki", "itou", "yamada"]
values = [87, 76, 69]
ary = [keys, values].transpose
h = Hash[*ary.flatten]
```

### 特定の範囲の要素を取得する

```
["a", "b", "c", "d", "e"][0..2]
=> ["a", "b", "c"]
```

### 配列からnilを取り除く

```
[1, 2, nil, 3, 4, nil].compact
=> [1, 2, 3, 4]
```

### 配列から特定の条件に一致する要素を取り除く

```
[1,2,3,4,5,6].delete_if { |x| x % 2 == 0 }
=> [1, 3, 5]
```

### 10進数を基数変換する

```
255.to_s(2)
=> "11111111"
255.to_s(8)
=> "377"
255.to_s(16)
=> "ff"
```

逆の操作を行う場合は`to_i`メソッドを使います。

```
"11111111".to_i(2)
=> 255
"377".to_i(8)
=> 255
"ff".to_i(16)
=> 255
```

### ハッシュのvalueでソートする

```
scores = { 'Carol' => 90, 'Alice' => 50, 'Bob' => 60, 'David' => 40 }
scores.sort {|(k1, v1), (k2, v2)| v2 <=> v1 }
=> [["Carol", 90], ["Bob", 60], ["Alice", 50], ["David", 40]]
```

### 同じ値を数える

```
["a", "b", "c", "a", "b", "b"].each_with_object(Hash.new(0)) {|r, arr| arr[r]+=1 }
=> {"a"=>2, "b"=>3, "c"=>1}
```

