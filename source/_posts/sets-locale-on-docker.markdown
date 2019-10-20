---
layout: post
title: "Dockerでlocaleを設定する"
date: 2016-08-24 17:32:51 +0900
comments: true
categories: docker
description: "Dockerでlocaleを設定したいときがある。例えばマルチバイトを扱うときだ。Dockerでlocaleを設定する場合は、以下のようにDockerfileに定義する。"
---

Dockerでlocaleを設定したいときがある。
例えばマルチバイトを扱うときだ。
localeを設定していないイメージで処理を行うと `ArgumentError: invalid byte sequence in US-ASCII` のようなエラーが発生することがある。
Dockerでlocaleを設定する場合は、以下のようにDockerfileに定義する。

```
RUN locale-gen ja_JP.UTF-8
ENV LANG ja_JP.UTF-8
ENV LANGUAGE ja_JP:en
ENV LC_ALL ja_JP.UTF-8
```

タイムゾーンも変えたい場合は以下も追記しておく。

```
RUN ln -fs /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
```

コンテナに入ってlocaleを表示すると以下のようになった。

```
root@e893a5fa3eea:/# locale
LANG=ja_JP.UTF-8
LANGUAGE=ja_JP:en
LC_CTYPE="ja_JP.UTF-8"
LC_NUMERIC="ja_JP.UTF-8"
LC_TIME="ja_JP.UTF-8"
LC_COLLATE="ja_JP.UTF-8"
LC_MONETARY="ja_JP.UTF-8"
LC_MESSAGES="ja_JP.UTF-8"
LC_PAPER="ja_JP.UTF-8"
LC_NAME="ja_JP.UTF-8"
LC_ADDRESS="ja_JP.UTF-8"
LC_TELEPHONE="ja_JP.UTF-8"
LC_MEASUREMENT="ja_JP.UTF-8"
LC_IDENTIFICATION="ja_JP.UTF-8"
LC_ALL=ja_JP.UTF-8
```
