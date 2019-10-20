---
layout: post
title: "capistrano/wheneverで cannot load such fileがでる"
date: 2016-06-15 13:42:55 +0900
comments: true
categories: ruby gem
description: "wheneverでLoadError: cannot load such fileとinstance variable @_env not definedがでた。その回避策とその後の記録です。"
---

wheneverを導入するため`Capfile`に `require "whenever/capistrano"`と定義して `bundle exec cap -T` とすると以下のエラーがでた。


```
LoadError: cannot load such file -- /Users/shoyan/app/vendor/bundle/ruby/2.2.0/gems/whenever-0.9.5/lib/whenever/tasks/whenever.rake

```

実際に`vendor/bundle/ruby/2.2.0/gems/whenever-0.9.5/lib/whenever/tasks/whenever.rake` を確認してみると、たしかにそのファイルが存在しない。

wheneverのリポジトリを見てみると、シンボリックリンクがはってあった。  

`bundle install` したときにはシンボリックリンクがはられないようだ。

手元のRubyのバージョンは、2.2.4だった。

ruby2.3.1で実行してみると `Gem::Package::PathError` が発生した。


```
ERROR:  While executing gem ... (Gem::Package::PathError)
    installing into parent path /Users/shoyan/.rbenv/versions/2.3.0/lib/ruby/gems/2.3.0/gems/capistrano/v3/tasks/whenever.rake of /Users/shoyan/.rbenv/versions/2.3.0/lib/ruby/gems/2.3.0/gems/whenever-0.9.5 is not allowed

```

このエラーはRuby2.3.2では直っていることが期待される。

* https://github.com/rubygems/rubygems/issues/1448#issuecomment-218355005

## 回避策

Capfileでの指定はやめて、`lib/capistrano/tasks` 以下にwhenever.taskを作成した。  
これでcapのタスク自体は実行できるようになった。  
しかし、ruby2.3.1の環境ではbundle installがこけるのでwheneverの導入自体ができないという状況。

## その後

bundle installがこけるバグは以下のPRがでており0.9.6で修正されていた。

* https://github.com/javan/whenever/pull/633

また、0.9.5では `cap whenever:update_crontab` で `instance variable @_env not defined` が発生してコマンドが正常に実行できないバグがあったが、0.9.7で修正されている。

* https://github.com/javan/whenever/issues/634

実はこのバグは自分が踏んでおり、その修正案をPRしてCHANGELOGに名前が刻まれた。

* https://github.com/javan/whenever/pull/636
* https://github.com/javan/whenever/commit/5aa0ccfa53e87bda43e92da005d781a4b5a8d5a5
