---
layout: post
title: "Dockerでsystemctlでserviceが起動できない"
date: 2016-04-14 20:10:46 +0900
comments: true
categories: Docker
---
centos7からsystemctlでserviceを起動するようになったが、Dockerで起動すると「Failed to get D-Bus connection: No connection to service manager.」というエラーメッセージがでて起動できないという問題が起こった。


```
$ docker run -it centos:centos7 /bin/bash
# systemctl
Failed to get D-Bus connection: No connection to service manager.

```

サービスを起動するためには以下の方法でコンテナを起動すればよい。

```
$ docker run --privileged -d --name httpd centos:centos7 /sbin/init

```

起動したコンテナでhttpdをinstallして起動する。


```
$ docker exec -it httpd /bin/bash
# yum install httpd -y
# systemctl enable httpd.service
# systemctl start httpd.service

```

プロセスを確認。起動できている。


```
ps aux | grep apache

bash-4.2# ps aux | grep apache
apache     168  0.0  0.3 221912  4028 ?        S    05:41   0:00 /usr/sbin/httpd -DFOREGROUND
apache     169  0.0  0.3 221912  4028 ?        S    05:41   0:00 /usr/sbin/httpd -DFOREGROUND
apache     170  0.0  0.3 221912  4028 ?        S    05:41   0:00 /usr/sbin/httpd -DFOREGROUND
apache     171  0.0  0.3 221912  4028 ?        S    05:41   0:00 /usr/sbin/httpd -DFOREGROUND
apache     172  0.0  0.3 221912  4028 ?        S    05:41   0:00 /usr/sbin/httpd -DFOREGROUND
root       180  0.0  0.0   9044   808 ?        S+   05:49   0:00 grep apache

```

yunanoさんの記事が大変参考になった。  

* [CentOS 7のDockerコンテナ内でsystemdを使ってサービスを起動する](http://qiita.com/yunano/items/9637ee21a71eba197345)

## Dockerについて学ぶ

Dockerについてはいくつか書籍が出ているが、[プログラマのためのDocker教科書 インフラの基礎知識&コードによる環境構築の自動化](http://amzn.to/2qiMHAN)が良さそうに思う。
目次を見てみると、インフラの基礎知識からDockerfileを使ったDockerの構築、Docker Composeの使い方、Docker Swarmを使ったマルチホスト環境でのDocker運用まで網羅してある。
また、Dockerの基礎的なことに加え、インフラの基礎とコンテナ仮想化技術についても説明してあるのでDockerを学びつつもインフラについても学べそうだ。

### 目次

第1章: 抑えておきたいシステム / インフラの知識
第2章: コンテナ仮想化技術とDocker
第3章: Dockerのインストールと基本コマンド
第4章: Dockerfileを使ったコードによるサーバ構築
第5章: Dockerイメージの共有 - Docker Registry
第6章: 複数コンテナの一元管理 - Docker Compose
第7章: マルチホスト環境でのDocker運用 - Docker Machine, Docker Swarm
第8章: クラウドでのDocker運用

<a href="https://www.amazon.co.jp/%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9E%E3%81%AE%E3%81%9F%E3%82%81%E3%81%AEDocker%E6%95%99%E7%A7%91%E6%9B%B8-%E3%82%A4%E3%83%B3%E3%83%95%E3%83%A9%E3%81%AE%E5%9F%BA%E7%A4%8E%E7%9F%A5%E8%AD%98-%E3%82%B3%E3%83%BC%E3%83%89%E3%81%AB%E3%82%88%E3%82%8B%E7%92%B0%E5%A2%83%E6%A7%8B%E7%AF%89%E3%81%AE%E8%87%AA%E5%8B%95%E5%8C%96-%E9%98%BF%E4%BD%90-%E5%BF%97%E4%BF%9D/dp/479814102X/ref=as_li_ss_il?ie=UTF8&qid=1495788976&sr=8-1&keywords=docker&linkCode=li3&tag=syoyama-22&linkId=057e48e0a549d45b25dd7dca15a6eef7" target="_blank"><img border="0" src="//ws-fe.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=479814102X&Format=_SL250_&ID=AsinImage&MarketPlace=JP&ServiceVersion=20070822&WS=1&tag=syoyama-22" ></a><img src="https://ir-jp.amazon-adsystem.com/e/ir?t=syoyama-22&l=li3&o=9&a=479814102X" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />

## Dockerの記事一覧

SHOYAN BLOGではDockerについていくつか記事を書いているので興味があれば見てほしい。

- [Macを引っ越ししたらDockerデーモンが起動しなくなった](/blog/2016/10/05/fix-unable-to-connect-to-docker-daemon/) 
- [docker-composeを使ってPHPコンテナとMySQLコンテナを連携させる](/blog/2016/09/27/links-container-with-docker-compose/) 
- [Dockerのコンテナ間を繋ぐLinksを使ってPHPとMySQLコンテナを連携させる](/blog/2016/09/16/links-container-for-docker/) 
- [Dockerでlocaleを設定する](/blog/2016/08/24/sets-locale-on-docker/) 
- [GitHubにpushしたらDockerイメージを自動ビルドする](/blog/2016/08/03/docker-auto-build-tutorial/) 
- [Dockerのコンテナでyum Installが失敗する](/blog/2016/05/30/yum-install-failed-by-insufficient-space-on-docker/) 
- [DockerコンテナにChefを流してみた](/blog/2016/04/21/nginx-and-ruby-on-docker/) 
- [Dockerでno Space Left on Deviceが出てbuildできなくなった](/blog/2016/04/13/no-space-left-on-device-on-docker/) 

## Dockerのサンプルコード

githubでDockerを使ったサンプルコードを公開しているのでこちらも参考にしてほしい。

DockerでRubyアプリケーションをホスティングするサンプルコード
* [theme-builder](https://github.com/shoyan/theme-builder)

docker-composeを使ってPHPコンテナとMySQLコンテナを連携させるサンプルコード
* [docker-compose-with-link-sample](https://github.com/shoyan/docker-compose-with-link-sample)

nginxとrubyをChefを使ってインストールするサンプルコード
* [nginx-and-ruby-on-docker](https://github.com/shoyan/nginx-and-ruby-on-docker)
