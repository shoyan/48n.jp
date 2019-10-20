---
layout: post
title: "TCPWrappersとは何なのかを調べてみた"
date: 2016-06-06 19:33:59 +0900
comments: true
categories: linux
description: "sshのハンドシェイクでこけてしまい、その原因がわからずハマりました。  原因としては、TCPWrappersで拒否されていました。TCPWrappersとはなんだろうということで調べてみました。"
---

sshのハンドシェイクでこけてしまい、その原因がわからずハマりました。  
原因としては、TCPWrappersで拒否されていました。  
TCPWrappersとはなんだろうということで調べてみました。

## TCPWrappersとは

TCPWrapperとは、ネットワークのアクセス制御をする機能です。
デフォルトでインストールされています。

## Linuxの基本的なアクセス制御の仕組み

![Access Control to Network Services](https://www.centos.org/docs/5/html/Deployment_Guide-en-US/images/tcp-wrappers/tcp_wrap_diagram.png)

アクセス制御の仕組みとして、FirewallとTCP Wrappersがあります。  
Firewallはiptablesで制御します。
なぜ2つの仕組みが存在するのかはわかりません（誰か教えてください）。

クライアント→Firewall→TCPWrappers→サービスという流れで処理を行います。  
TCPWrappersは接続を許可するかどうかのファイルを参照し、許可リストにあればサービスに処理を受け渡します。

処理のログはsyslog daemon (syslogd)によってクライアントと接続先サービスの情報を `/var/log/secure` または `/var/log/messages` に書き込まれます。

## TCPWrappersのアドバンテージ

TCPWrappersを使うことで、以下のメリットがあるとのこと。

- 接続したクライアントとそのサービスがログに残る
- 様々なプロトコルを集約して管理できる

##TCPWrappersの設定ファイル

以下の2つのファイルがあります。

- /etc/hosts.allow
- /etc/hosts.deny

TCPWrappersはクライアントからのリクエストを受けると、以下の処理を行います。

- `/etc/hosts.allow` を参照します。クライアントがリストに存在した場合、接続を許可します。リストに存在しない場合は次のステップに進みます。
- `/etc/hosts.deny`を参照します。クライアントがリストに存在した場合、接続を拒否します。リストにない場合は接続を許可します。

### 注意点

- `/etc/hosts.allow`から先に評価され、そこでリストに一致した場合`/etc/hosts.deny`は評価されません。`/etc/hosts.allow`と`/etc/hosts.deny`に同じクライアントを指定した場合は`/etc/hosts.allow`が優先されます。
- ファイルの上から評価されていき、一致した時点で評価を打ち切ります。順番が重要です。
- TCPWrappersはキャッシュを持ちません。ですので、設定ファイルを書き換えたら即反映されます。デーモンをリスタートする必要はありません。
- 最後に改行を含めるとエラーメッセージが出るとのことなので、含めないほうがよさそうです。`warning: /etc/hosts.allow, line 20: missing newline or line too long`というメッセージが `/var/log/messages` または `/var/log/secure`に出力されるとのことです。

## 参考リンク

- [【第6回】TCPWrappers(hosts.allow,hosts.deny)とSSHの公開鍵認証について](http://lpi.or.jp/column/linux/linux_m06.shtml)
- [42.5. TCP Wrappers and xinetd](https://www.centos.org/docs/5/html/Deployment_Guide-en-US/ch-tcpwrappers.html)
