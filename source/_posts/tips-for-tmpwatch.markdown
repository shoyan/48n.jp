---
layout: post
title: "tmpwatchでさくっと不要なファイルを消す"
date: 2016-07-08 13:49:27 +0900
comments: true
categories: linux
description: "古いファイルを消したいときにtmpwatchを使うと簡単に消すことができます。例えば、/var/www/app/tmp 配下の48時間以上前のファイルやディレクトリを消したい場合は以下で消せます。tmpwatch -m 48  /var/www/app/tmp"
---

古いファイルを消したいときにtmpwatchを使うと簡単に消すことができます。

例えば、`/var/www/app/tmp` 配下の48時間以上前のファイルやディレクトリを消したい場合は以下で消せます。

```
tmpwatch -m 48  /var/www/app/tmp
```

tmpwatchは再帰的にファイルを削除するので、サブディレクトリがあればその配下のファイルも削除されます。
シンボリックの場合はリンクが削除されるだけでリンク先のファイルは削除されないとのことです。

### 参考リンク

- [tmpwatchが便利！](http://spring-mt.tumblr.com/post/18484633412/tmpwatch%E3%81%8C%E4%BE%BF%E5%88%A9)
