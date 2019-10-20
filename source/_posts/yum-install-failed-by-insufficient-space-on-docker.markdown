---
layout: post
title: "Dockerのコンテナでyum installが失敗する"
date: 2016-05-30 12:58:52 +0900
comments: true
categories: docker
description: "Dockerのコンテナでyum installが失敗する。Insufficient space in download directory /var/cache/yum/x86_64/6/updates/packagesという容量不足のエラーがでていた。不要なコンテナを消したら解決した。"
---

Dockerのコンテナでyum installが失敗する。  
以下のようなエラーがでていた。  


```
Insufficient space in download directory /var/cache/yum/x86_64/6/updates/packages

```

容量が不足しているらしい。

ディスク容量を確認したところ、100%になっていた。


```
bash-4.2# df
Filesystem     1K-blocks     Used Available Use% Mounted on
none            19049892 18226752         0 100% /
tmpfs             509992        0    509992   0% /dev
tmpfs             509992        0    509992   0% /sys/fs/cgroup
/dev/sda1       19049892 18226752         0 100% /etc/hosts
shm                65536        0     65536   0% /dev/shm

```

不要なコンテナが溜まっていて、そのせいでディスク容量を圧迫していたようだ。

### コンテナを消す方法

* docker ps -a でコンテナの一覧が表示される
* docker rm  container_id で消す

コンテナを消すと、ディスク容量に空きができてyum installできるようになった。

こちらは類似案件。

* [Dockerでno Space Left on Deviceが出てbuildできなくなった](http://shoyan.github.io/blog/2016/04/13/no-space-left-on-device-on-docker/)
