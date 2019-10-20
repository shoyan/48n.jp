---
layout: post
title: "PHPの構文チェックを自動で行う方法"
date: 2016-08-08 13:30:30 +0900
comments: true
categories: php
description: "私のチームではCIで自動的にLINTをする仕組みを構築しています。この仕組みにより、レガシーコードでテストコードがない環境でもシンタックスエラーの混入を防ぐことができます。"
---

私のチームではCIで自動的にLINTをする仕組みを構築しています。
この仕組みにより、レガシーコードでテストコードがない環境でもシンタックスエラーの混入を防ぐことができます。

中身はシンプルで以下のスクリプトを作成して、CIプラットホームで実行するようにします。

php-lint.sh

```
#!/bin/sh

RESULT=`find . -type f -name "*.php" -exec php -l {} \; 2>&1 | grep "PHP Parse error"`

if [ "$RESULT" != "" ];then
    echo "$RESULT"
    exit 1
fi
```

例えば、Droneでチェックする場合は以下のように設定します。

.drone.yml

```
script:
  - ./php-lint.sh
```
