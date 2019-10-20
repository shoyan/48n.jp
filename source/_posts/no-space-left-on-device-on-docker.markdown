---
layout: post
title: "Dockerでno space left on deviceが出てbuildできなくなった"
date: 2016-04-13 16:14:05 +0900
comments: true
categories: Docker
---

Dockerのbuild時に以下のエラーが発生するようになった。


```
$  docker build -t docker-and-chef .
Sending build context to Docker daemon 131.6 kB
Error response from daemon: mkdir /mnt/sda1/var/lib/docker/tmp/docker-builder670782655: no space left on device

```

PCを再起動してみても直らず、どうしたものかとググっていたら以下の情報を見つけた。
http://stackoverflow.com/questions/30604846/docker-error-no-space-left-on-device

やることは以下

* `docker ps -a`して表示されたコンテナを消す。
* `docker images` して表示されたイメージを消す。

コンテナは`docker rm container_id`で消すことができる。  
イメージは`docker rim image_id`で消すことができる。

VMのディスク容量がないときに発生するエラーのようだ。  
不要なコンテナがたくさんあったので、それらのコンテナを消すとエラーはでなくなった。
