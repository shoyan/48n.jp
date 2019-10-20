---
layout: post
title: "Shellで日本語ドメインをPunycode(IDNドメイン)に変換する方法"
date: 2016-06-02 19:37:47 +0900
comments: true
categories: linux
description: "Shellで日本語ドメインをIDNフォーマットに変換する方法を紹介します。libidnというGNUのライブラリがあるので、それを使います。対象のOSはMacです。"
---

Shellで日本語ドメインをIDNフォーマットに変換する方法を紹介します。  
対象のOSはMacです。

[libidn](http://www.gnu.org/software/libidn/)というGNUのライブラリがあるので、それを使います。

## libidnのインストール

homebrewでインストールします。


```
$ brew install libidn

```

インストールしたら、idnコマンドが使えるようになります。


```
$ idn
libidn 1.32
Copyright 2002-2015 Simon Josefsson.
GNU Libidn is free software with ABSOLUTELY NO WARRANTY.  For more
information about these matters, see <http://www.gnu.org/licenses/>.
Type each input string on a line by itself, terminated by a newline character.

idn: tld_check_4z: Missing input

```

## libidnでPunycodeに変換する

以下のようにPunycodeに変換できます。


```
$ idn しょーやん.xyz
xn--68jwei3c27a.xyz

```

unicodeに変換する場合は`-u`オプションを使います。


```
$ idn -u xn--68jwei3c27a.xyz
しょーやん.xyz

```

## digとidnを組み合わせて使う


```
⇒  dig `idn しょーやん.xyz` NS

; <<>> DiG 9.8.3-P1 <<>> xn--68jwei3c27a.xyz NS
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 59533
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;xn--68jwei3c27a.xyz.   IN  NS

;; ANSWER SECTION:
xn--68jwei3c27a.xyz.  3578  IN  NS  dns01.muumuu-domain.com.
xn--68jwei3c27a.xyz.  3578  IN  NS  dns02.muumuu-domain.com.

;; Query time: 34 msec
;; SERVER: 192.168.74.20#53(192.168.74.20)
;; WHEN: Fri Jun  3 09:25:40 2016
;; MSG SIZE  rcvd: 94)>>>>>>

```
