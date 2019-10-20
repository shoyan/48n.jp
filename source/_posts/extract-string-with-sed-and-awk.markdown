---
layout: post
title: "sedやawkを使ってテキストから必要な列のみ取得する"
date: 2016-06-29 17:01:36 +0900
comments: true
categories: linux
description: "文字列からsedやawkを使ってlabelだけとるshell芸を紹介します。"
---

以下のような文字列(ファイルに保存されているとする)からsedやawkを使ってlabelだけとるshell芸を紹介します。

```
+-------------------------+-------+
| label                   | state |
+-------------------------+-------+
| A                       |     0 |
| B                       |     0 |
| C                       |     0 |
| D                       |     0 |
| E                       |     0 |
| F                       |     0 |
| G                       |     0 |
| H                       |     2 |
| I                       |     0 |
| J                       |     0 |
| K                       |     0 |
+-------------------------+----———+
```

### label列のみ取得

```
⇒  sed -n '4,14p' table.txt | awk '{print $2}'
A
B
C
D
E
F
G
H
I
J
K
```

### スペース区切りに加工する

```
⇒  sed -n '4,14p' table.txt | awk '{print $2 " "}' | tr -d '\n'
A B C D E F G H I J K
```

`sed -n '4,14p’` で指定した行数のみ取得しています  
次に`awk '{print $2}’` でlabel列のみ抽出しています。  
改行を消すのは `tr -d '\n’` が便利です。
