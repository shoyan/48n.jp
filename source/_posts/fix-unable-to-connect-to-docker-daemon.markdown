---
layout: post
title: "Macを引っ越ししたらDockerデーモンが起動しなくなった"
date: 2016-10-05 14:27:46 +0900
comments: true
categories: docker
description: "Macを引っ越ししたらDockerデーモンが起動しなくなった。Dockerを起動しようとすると Cannot connect to the Docker daemon. Is the docker daemon running on this host? というメッセージがでて起動しない。"
---

Macを引っ越ししたらDockerデーモンが起動しなくなった。
Dockerを起動しようとすると `Cannot connect to the Docker daemon. Is the docker daemon running on this host?` というメッセージがでて起動しない。
docker-machineコマンドで確認してみると以下のエラーが出ていた。

```
$ docker-machine ls
NAME      ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER    ERRORS
default   -        virtualbox   Running   tcp://192.168.99.100:2376           Unknown   Unable to query docker version: Cannot connect to the docker engine endpoint
```

1度docker-machineコマンドで既存のDockerを削除して作成するとうまくいった。

```
$ docker-machine rm -f default
$ docker-machine create -d virtualbox default
```

今度はDockerのバージョンがv1.12.1と表示されている。

```
⇒  docker-machine ls
NAME      ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER    ERRORS
default   -        virtualbox   Running   tcp://192.168.99.100:2376           v1.12.1
```

ちなみに既存のDockerを削除すると今までのimageは消えてしまう。

Docker for Macがでてそっちに移行した方がよさげな機運を感じるので、そろそろDocker ToolboxからDocker for Macに移行する時期なのかな。

* https://docs.docker.com/docker-for-mac/docker-toolbox/
