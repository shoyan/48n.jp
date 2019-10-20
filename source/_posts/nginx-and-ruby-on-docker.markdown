---
layout: post
title: "DockerコンテナにChefを流してみた"
date: 2016-04-21 18:19:01 +0900
comments: true
categories: Docker Chef
---
Chefのレシピを書くとき、Dockerコンテナにレシピを流せると気軽に確認ができてよいなと思い、DockerコンテナにChefを流せるようにしてみました。

リポジトリを作ったので参考にどうぞ。  
https://github.com/shoyan/nginx-and-ruby-on-docker

### Install
DockerとChefをインストールしている必要があるので、以下からインストールしてください。

Docker

- https://www.docker.com/products/docker-toolbox

Chef

- https://downloads.chef.io/chef-client/

### DockerコンテナにChefを流す
まずは、Docker QuickStart TerminalをクリックしてDockerを起動します(アプリケーションにあります)。

Dockerを起動したらNginxとrubyをインストールするCookbookを実行します。


```
$ git clone https://github.com/shoyan/nginx-and-ruby-on-docker
$ cd nginx-and-ruby-on-docker

```

以下のコマンドでimageをbuildします。


```
$ docker build -t nginx-and-ruby .

```

imageを確認


```
$  docker images
REPOSITORY             TAG                            IMAGE ID            CREATED             SIZE
nginx-and-ruby         latest                         6b4d5602119f        36 seconds ago      382.2 MB

```

Dockerのコンテナを起動します。  
image_idは docker imagesのIMAGE IDを指定します。  
今回の場合は6b4d5602119fです(場合により変わります)。


```
$ docker run --privileged -d --name nginx-and-ruby image_id /sbin/init

```

--privilegedはCentOS7のimageでserviceコマンドを起動するおまじないです。  
[Dockerでsystemctlでserviceが起動できない](http://shoyan.github.io/blog/2016/04/14/start-systemctl-on-docker/)

起動したDockerのコンテナにログインします。


```
$ docker exec -it nginx-and-ruby bash

```

ログインしたらDockerコンテナでChefを実行します。


```
# chef-client -z -j nodes/bootstrap.json -c client.rb

```

Dockerを使うとChefを実行→コンテナを消す→真っさらな状態からもう一度Chefを流すというサイクルが気軽にできるので便利です。

もう一度真っさらな状態のコンテナを作りたいときは、以下のようにします。


```
$ doker ps
CONTAINER ID        IMAGE                  COMMAND              CREATED             STATUS                        PORTS                                           NAMES
17a11c4b6ce8        6b4d5602119f           "/sbin/init"         2 hours ago         Exited (137) 12 seconds ago                                                   nginx-and-ruby

# Dockerコンテナを消す
$ docker rm -f 17a11c4b6ce8

# dockerコンテナを起動する
$ docker run --privileged -d --name nginx-and-ruby image_id /sbin/init

```

https://github.com/shoyan/nginx-and-ruby-on-docker
