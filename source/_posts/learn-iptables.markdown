---
layout: post
title: "iptablesことはじめ"
date: 2016-07-15 14:05:31 +0900
comments: true
categories: linux
description: "iptablesの概念と設定方法を調べました。iptablesとはパケットをフィルタリングするためのツールです。また、NATとしても使えます。また、iptablesを理解するにあたって必要な用語もまとめています。"
---

iptablesの概念と設定方法を調べました。

## iptablesとは

iptablesとはパケットをフィルタリングするためのツールです。
また、NATとしても使えます。

## iptablesの用語

iptablesを理解するにあたって必要な用語をまとめました。

### TARGETS

iptablesのルールを設定する際に、targetを指定します。
targetはユーザー定義のものと特別なtargetである、ACCEPT, DROP, QUEUE or RETURNがあります。
パケットに対してどんな操作をするかを定義します。

代表的なtargetです。

ACCEPT
パケットを通す
DROP
パケットを破棄する
QUEUE
パケットをキューにためる
RETURN
チェインを辿るのをストップして、評価を行う
REJECT
パケットを破棄し、エラーパケットを返す
DNAT
送信先IPアドレスを変更する
SNAT
送信元IPアドレスを変更する
MASQUERADE
複数の送信元IPアドレスを変更（マスカレード）する
LOG
ログを出力する

### Chain

iptablesを設定する際にchainを指定します。
chainにはビルトインchainとユーザー定義chainがあります。
chainはパケットに対する実行条件を定義します。

ビルトインchainは以下です。

INPUT
FORWARD
OUTPUT
PREROUTING
POSTROUTING

### TABLES

一般的にはフィルタリングとして使われることが多いiptablesですが、他にも様々な機能があります。
iptablesにはそれをtableという概念で扱っており、5つのtableがあります。

*filter*
パケットのフィルタリングを設定するためのテーブルです。
デフォルトのテーブルです。tableオプションを明示的に指定しない場合は、filterが指定されたことになります。
INPUT、FORWARD、OUTPUTのChainを含みます。

*nat*
IPアドレスの変換を設定するためのテーブルです。
PREROUTING、POSTROUTING のChainを含みます。

*mangle*
パケットの書き換えを設定するためのテーブルです。

*raw*
接続の追跡の除外の設定をするためのテーブルです。

*security*
アクセスコントロールの設定をするためのテーブルです。

## OPTIONS

iptablesのコマンドにオプションを指定できます。
オプションはいくつかのグループに区別されます。

### COMMANDS

iptablesに実行してほしいコマンドを指定します。
以下のようなコマンドがあります。

-A, --append
選択したチェインにルールを追加します。

-C, --check
指定したチェインにルールが存在するかをチェックします。

-D, --delete
チェインからルールを消します。

詳細はiptablesのmanをご覧ください。

### PARAMETERS

ルールを作成する際に指定するパラメーターです。

-p, --protocol
ルールの対象となるプロトコルを指定します。

-j, --jump
ルールの対象となるtargetを指定します。

-s, --source
ルールの対象となるアドレスを指定します。
例えば、IPアドレス、ホスト名、ネットワーク名などを指定します。

詳細はiptablesのmanをご覧ください。

### OTHER OPTIONS

追加オプションです。

-v, --verbose
詳細表示のオプションです。

詳細はiptablesのmanを参照ください。

## iptablesの基本的な構文

基本的には以下のように指定します。

```
iptables [-t table] command chain options target
```

例えば、ローカルホストからのping(icmp)を許可する場合は以下となります。

```
iptables -A INPUT -p icmp -j ACCEPT
```

## iptablesの基本的な読み方

現在のiptablesの設定を表示するには、iptables -L コマンドを使います。

```
Example: Input Chain Rule Table Listing
Chain INPUT (policy DROP)
target     prot opt source               destination
ACCEPT     all  --  anywhere             anywhere             ctstate RELATED,ESTABLISHED
ACCEPT     all  --  anywhere             anywhere
DROP       all  --  anywhere             anywhere             ctstate INVALID
UDP        udp  --  anywhere             anywhere             ctstate NEW
TCP        tcp  --  anywhere             anywhere             tcp flags:FIN,SYN,RST,ACK/SYN ctstate NEW
ICMP       icmp --  anywhere             anywhere             ctstate NEW
REJECT     udp  --  anywhere             anywhere             reject-with icmp-port-unreachable
REJECT     tcp  --  anywhere             anywhere             reject-with tcp-reset
REJECT     all  --  anywhere             anywhere             reject-with icmp-proto-unreachable
```

1行目がChainの名前で、次にデフォルトポリシーが表示されています(DROP)。
2行目がカラム名です。

*target*: target名
*prot*: プロトコル名。例えば、tcp, udp, icmp, or all
*opt*: めったに使われません。
*source*: 接続元のIPアドレス or サブネット or anywhere
*destination*: 接続先のIPアドレス or サブネット or anywhere

## iptablesコマンドの使い方

いくつかの例を紹介します。

### 特定のサーバーからのhttp通信を許可する

```
# iptables -A INPUT -p tcp -s 192.168.1.1 --dport 80 -j ACCEPT
# 設定を保存
# service iptables save
```

### ルールを消す

iptables -D ルール で消すことができます。
`iptables -S` でルール一覧がでるので、そのルールを指定すれば簡単です。

```
# iptables -S
-A INPUT -s 157.7.105.69/32 -p tcp -m tcp --dport 80 -j ACCEPT
```

以下のコマンドで消します。

```
iptables -D INPUT -s 157.7.105.69/32 -p tcp -m tcp --dport 80 -j ACCEPT
```

## 参考文献

* [Man page of IPTABLES](http://linux.die.net/man/8/iptables)
* [日本語訳](https://linuxjm.osdn.jp/html/iptables/man8/iptables.8.html)
* [iptablesの読み方覚え書き](http://qiita.com/kmikmy/items/d279b1b993661ba7dbe4)
* [iptablesの設定 入門編](http://murayama.hatenablog.com/entry/20100206/1265444193)
* [iptablesの設定方法](https://help.sakura.ad.jp/app/answers/detail/a_id/2423/~/iptables%E3%81%AE%E8%A8%AD%E5%AE%9A%E6%96%B9%E6%B3%95)
* [How To List and Delete Iptables Firewall Rules](https://www.digitalocean.com/community/tutorials/how-to-list-and-delete-iptables-firewall-rules)
