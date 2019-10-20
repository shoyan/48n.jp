---
layout: post
title: "docker-composeを使ってPHPコンテナとMySQLコンテナを連携させる"
date: 2016-09-27 14:04:11 +0900
comments: true
categories: docker
description: "以前、Dockerコンテナを連携させる方法を書いたが、コンテナ間の連携が必要な際はdocker-composeを使うと便利だ。今回は前回と同じようにPHPとMySQLのコンテナを連携させるが、その連携にdocker-composeを使った方法を紹介する。"
---

以前、[Dockerコンテナを連携させる方法](/blog/2016/09/16/links-container-for-docker/)を書いたが、コンテナ間の連携が必要な際はdocker-composeを使うと便利だ。
今回は前回と同じようにPHPとMySQLのコンテナを連携させるが、その連携にdocker-composeを使った方法を紹介する。

まずは、適当なディレクトリをつくる。
今回は `docker-compose-sample`とする。

`docker-compose-sample/` 配下にDockerfileを作成する。

```
FROM shoyan/www-ci
ADD . /app
WORKDIR /app
ENV MYSQL_HOST mysql
ENV MYSQL_PASSWORD ''
```

次に `docker-compose.yml`を作成する。

```
version: '2'
services:
  web:
    build: .
    links:
      - mysql:mysql
  mysql:
    image: mysql:5.5
    environment:
      MYSQL_ALLOW_EMPTY_PASSWORD: 'yes'
    expose:
      - "3306"
    volumes:
      - ./docker-entrypoint-initdb.d:/docker-entrypoint-initdb.d
```

servicesにwebとmysqlという名前でコンテナを定義している。

### webコンテナ
webコンテナはDockerfileをbuildして、mysqlコンテナと連携させる定義をしている。
これによりwebコンテナからはmysqlという名前でmysqlコンテナにアクセスできるようになる。

### mysqlコンテナについて
MySQLは公式のイメージを使っている。
他のコンテナからアクセスできるようにexposeで3306ポートを指定している。

公式のMySQLイメージから作成したコンテナは`/docker-entrypoint-initdb.d/` 配下にあるシェルスクリプトやsqlファイルを起動時に実行するようになっている。
今回はデータベースとテーブルを作成するためにこの機構を利用する。
volumesを利用してファイルを配置している。

以下の`docker-compose-sample/docker-entrypoint-initdb.d/setup.sql` を作成しておく。

```sql
CREATE DATABASE IF NOT EXISTS app_test;

DROP TABLE IF EXISTS `app_test`.`user`;
CREATE TABLE `app_test`.`user` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `name` varchar(8) DEFAULT NULL,
  `status` tinyint(1) DEFAULT NULL,
  `email` varchar(64) DEFAULT NULL,
  `postal_code` char(7) DEFAULT '',
  `character_text` text,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

### 確認用スクリプト

DBにアクセスできているかを確認するためのスクリプト `connect.php` を用意しておく。

```php
<?php
$dsn = sprintf('mysql:host=%s:3306;dbname=%s',  $_ENV['MYSQL_HOST'], 'app_test');
$user = 'root';
$password = $_ENV['MYSQL_PASSWORD'];
$dbh = new PDO($dsn, $user, $password);
$sql = "SELECT version();";
foreach ($dbh->query($sql, PDO::FETCH_ASSOC) as $row) {
    print_r($row);
}
$sql = "show tables;";
foreach ($dbh->query($sql, PDO::FETCH_ASSOC) as $row) {
    print_r($row);
}
```

### docker-composeでコンテナを起動する

準備ができたのでdocker-composeコマンドでコンテナを起動する。
以下のようにネットワークとコンテナが作成される。
`-d` オプションはバックグラウンドでコンテナを起動するために指定している。

```text
$ docker-compose up -d
Creating network "dockercomposesample_default" with the default driver
Creating dockercomposesample_mysql_1
Creating dockercomposesample_web_1
```

コンテナにログインしてみる。

```
$ docker run --rm -it -v `pwd`:/app --net=dockercomposesample_default dockercomposesample_web bash
```

確認用スクリプトで疎通を確認する。
バージョンとtableが表示されたら成功だ。

```
root@6237502e4401:/app# php connect.php
Array
(
    [version()] => 5.5.51
)
Array
(
    [Tables_in_app_test] => user
)
```

コンテナを削除するには `docker-compose down` コマンドを使う。

```
⇒  docker-compose down
Stopping dockercomposesample_mysql_1 ... done
Removing dockercomposesample_web_1 ... done
Removing dockercomposesample_mysql_1 ... done
Removing network dockercomposesample_default
```

サンプルコードをgithubで公開しているので参考にしてほしい。

* https://github.com/shoyan/docker-compose-with-link-sample
