---
layout: post
title: "Dockerのコンテナ間を繋ぐLinksを使ってPHPとMySQLコンテナを連携させる"
date: 2016-09-16 17:37:10 +0900
comments: true
categories: docker
description: "DockerはLinksというコンテナ間のネットワークを繋ぐ仕組みを持つ。例えば、アプリケーションコンテナとデータベースコンテナを連携して使いたいときに有用だ。例えば、3306ポートがEXPOSEされたmysqlがインストールされたコンテナと連携したいとする。"
---

DockerはLinksというコンテナ間のネットワークを繋ぐ仕組みを持つ。
例えば、アプリケーションコンテナとデータベースコンテナを連携して使いたいときに有用だ。
今回はMySQLとPHPのコンテナを連携させる方法を紹介する。

## MySQLコンテナの作成

まずは、mysqlコンテナを作成する。
バックグラウンドで起動させるための `--detach`オプションと3306ポートを解放するための `expose`オプションを指定している。

```
$ docker run --detach --expose=3306 --name=test-mysql --env="MYSQL_ROOT_PASSWORD=mypassword" mysql
```

以下のようにコンテナが起動していることを確認できるはずだ。

```
⇒  docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
b18a5159fe7c        mysql               "docker-entrypoint.sh"   1 minutes ago      Up 1 minutes       3306/tcp            test-mysql
```

## PHPコンテナの作成

次にPHPコンテナを作成する。
以下のDockerfileと確認用のconnect.phpを用意しておく。

Dockerfile

```
FROM php:7.1
RUN apt-get update
RUN docker-php-ext-install pdo_mysql mbstring
ADD . /app
```

connect.php

```php
<?php

$dsn = 'mysql:host=' . $_ENV['MYSQL_PORT_3306_TCP_ADDR'];
$user = 'root';
$password = $_ENV['MYSQL_ENV_MYSQL_ROOT_PASSWORD'];

$dbh = new PDO($dsn, $user, $password);

$sql = "SELECT version();";

foreach ($dbh->query($sql, PDO::FETCH_ASSOC) as $row) {
    print_r($row);
}
```

ビルドする。

```text
$ docker build -t mysql-php .
```

以下のように `--link 連携したいコンテナ名:エイリアス名` でPHPコンテナを起動すると、起動したコンテナの環境変数に連携したいコンテナに関する情報が登録される。

```
$ docker run -it --link test-mysql:mysql mysql-php bash
# root@9ec43f759596:/#
# env
MYSQL_PORT_3306_TCP_PORT=3306
MYSQL_PORT_3306_TCP=tcp://172.17.0.3:3306
MYSQL_ENV_MYSQL_VERSION=5.7.15-1debian8
MYSQL_NAME=/big_shirley/mysql
MYSQL_PORT_3306_TCP_PROTO=tcp
MYSQL_PORT_3306_TCP_ADDR=172.17.0.3
MYSQL_ENV_MYSQL_MAJOR=5.7
MYSQL_PORT=tcp://172.17.0.3:3306
```

最後にPHPのコンテナでconnect.phpを実行して接続できることを確認してみる。
version名が表示されれば成功だ。

```
# php /app/connect.php
=>
Array
(
    [version()] => 5.7.15
)
```
