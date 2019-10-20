---
layout: post
title: "C言語でHello World"
date: 2015-10-09 13:36:48 +0900
comments: true
categories: clang
---

たくさんのソフトウェアがC言語で作られており、自分もそういうソフトウェアの仕組みを知ったり自分で作れるようになりたいなと思って、C言語の勉強を始めました。

とりあえず、最初はC言語でHello worldをしてみます。  
環境はMac OSXです。  
X Codeをいれたら、gccが入るのでC言語はすぐ動くようでした。  

### gccがインストールされているかの確認

`gcc -v` と入力して、以下のように表示されたらOKです。

```
⇒  gcc -v
Configured with: --prefix=/Applications/Xcode.app/Contents/Developer/usr --with-gxx-include-dir=/usr/include/c++/4.2.1
Apple LLVM version 7.0.0 (clang-700.0.72)
Target: x86_64-apple-darwin14.5.0
Thread model: posix

```

### Hello Worldする
hello.cを作成して以下のように記入します。


```c
#include <stdio.h>
int main(int argc, char **argv)
{
    printf("hello world\n");
    return 0;
}

```

コンパイルします。

```
$ gcc hello.c -o hello

```

helloという実行ファイルができるので、実行します。

```
$ ./hello 
hello world

```
